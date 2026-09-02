---
title: "TDSQL 小表热点问题分析与优化"
date: 2026-09-03T10:00:00+08:00
draft: false
tags: ["TDSQL", "MySQL", "InnoDB", "Performance", "性能优化"]
categories: ["数据库"]
showToc: true
TocOpen: false
---

# TDSQL 小表热点问题分析与优化

> 账号表高并发查询，业务性能不及预期。本文记录一次从 **perf 火焰图 → threadpool 参数调优 → uprobe 内核级 tracing → B+ 树原理分析** 的完整性能问题定位过程。

## 问题描述

在 TDSQL 中，账号表存在高并发查询的场景，但业务性能始终不及预期。通过分析发现，系统存在严重的**调度开销**与**锁竞争**问题，QPS 远低于预期。

这张表的特点非常典型：

- **数据量极小**（两百多行，索引页总大小不足 10KB）；
- **并发查询量极大**（高 QPS 的点查场景）。

数据页几乎全部常驻 Buffer Pool，本应是"内存操作级别"的负载，却出现了性能瓶颈——多个线程反复竞争同一个 page（高频读写某几行或相近的数据），形成典型的**小表热点（hot small table）**问题。

## 问题分析

### 1. perf 热点：锁竞争 + 调度开销

Top 3 热点函数分别为：

| 热点函数 | 含义 |
| --- | --- |
| `native_queued_spin_lock_slowpath` | Linux 内核自旋锁（qspinlock，MCS 队列锁）的慢路径，说明内核锁竞争激烈 |
| `finish_task_switch` | 上下文切换完成后的收尾工作 |
| `__schedule` | 内核调度器主逻辑，线程被换出/换入 |

这三个函数同时霸榜，指向同一个结论：**大量线程在内核态自旋等待 + 频繁睡眠/唤醒**。线程在用户态拿不到锁后陷入内核等待（futex 路径会踩到运行队列锁、futex 队列锁等内核自旋锁），唤醒风暴又放大了调度开销——这是"锁排队（lock convoy）"的典型特征。

### 2. 火焰图：热点收敛到 `buf_page_optimistic_get`

进一步用火焰图下钻，调度/锁竞争主要由 TDSQL 内核函数 `buf_page_optimistic_get` 触发，**采样占比高达 72.94%**，说明高并发请求都在访问同一个 page，形成小表热点，导致性能不及预期。热点占比如此显著，可以判断系统已经越过性能拐点。

`buf_page_optimistic_get` 是 InnoDB 缓冲池中用于**乐观读取页面**的核心逻辑：

- 当线程尝试读取一个**未被修改**的页面时，先通过乐观锁（不进入等待队列）尝试获取页面；
- 若页面正被其他线程修改（如写操作）或状态校验失败，乐观路径失败，退化为悲观路径 `buf_page_get`（需要哈希查找、持有 block mutex，甚至等待 IO）。

因此，该函数的耗时直接反映了**页面级锁竞争的激烈程度**。接下来，我们通过调整 threadpool 相关参数（`size`、`oversubscribe`），粗略测量该小表的性能拐点。

## threadpool 参数对比与性能拐点

| threadpool_size × oversubscribe | 活跃线程数 | QPS |
| :---: | :---: | :---: |
| 16 × 1 | 16 | 7200 |
| **32 × 1** | 32 | **10400** |
| 16 × 3 | 48 | 8300 |
| 64 × 1 | ~90 | 6000 |
| 32 × 3 | 100+ | 5800 |

参数含义：

- `threadpool_size`：线程池的线程组数量，通常等于 CPU 核数，控制同时处理任务的线程规模；
- `threadpool_oversubscribe`：线程池的"超订阅"系数（默认为 3），限制每个线程组内允许同时活跃的线程数，用于平衡线程利用率与上下文切换开销。

**结论：** 活跃线程数 = 32（约 2×CPU 核数）时 QPS 达到峰值 10400；继续增大并发，QPS 反而下降近一半。

这背后的逻辑与热点函数相互印证：

1. 高并发点查总是命中**同一个 page**，活跃线程越多，`buf_page_optimistic_get` 中对该 page 锁字（lock word）的原子竞争越激烈；
2. 即使是共享的 S 锁，其加锁操作也是对同一缓存行的原子写——多核之间会产生 **cache line 弹跳（bouncing）**，真正的瓶颈从"数据"变成了"锁变量所在的缓存行"；
3. 线程数超过拐点后，新增线程不再提供并行度，只会在锁队列上排队、睡眠、唤醒，拉高 `__schedule`/`finish_task_switch` 的调度开销——这正是 **Amdahl 定律与通用扩展性定律（USL）** 中"一致性开销项"主导阶段的典型表现。

既然增大并发反而加剧对同一 page 的竞争，那么是否可以通过**添加字段把访问打散到更多 page** 上？容易想到的办法是增加一些 dummy 字段（无业务含义）。

但实测显示：**QPS 与热点分布均无显著差异**。为什么？下文的 uprobe tracing 与深入分析给出了答案。

## uprobe tracing：定位热点的决定性证据

### 函数签名与 page 标识

```cpp
// storage/innobase/include/buf0buf.h
bool buf_page_optimistic_get(
    ulint rw_latch,
    buf_block_t *block,
    uint64_t modify_clock,
    Page_fetch fetch_mode,
    const char *file, ulint line, mtr_t *mtr);
```

第 2 个参数 `block` 类型为 `buf_block_t`，其首字段 `page` 表示一个 buffer page；`page_id_t` 的前 2 个字段 `m_space`、`m_page_no` 唯一标识一个 buffer page，即二元组 `(space, page_no)`，且这两个字段均为 4 字节（`uint32_t`）：

```cpp
struct buf_block_t {
  buf_page_t page;  // 首字段是 buf_page_t 类型的 page 成员
  ...
};

struct buf_page_t {
  page_id_t id;     // 首字段是 page_id_t 类型的 id 成员
  ...
};

struct page_id_t {
  space_id_t m_space;  // uint32_t（4字节）
  page_no_t m_page_no; // uint32_t（4字节）
  ...
};

typedef uint32_t space_id_t;
using page_no_t = uint32_t;
```

这个内存布局是本次 tracing 的关键：**从 `block` 指针偏移 0 和 4 处，就能直接读出 page 的二元组标识**。

### uprobe 命令

```sh
./uprobe 'p:/data/tdsql_run/4001/mysql-server-8.0.24/bin/mysqld:_Z23buf_page_optimistic_getmP11buf_block_tm10Page_fetchPKcmP5mtr_t space=+0(%si):s32 page_number=+4(%si):s32'
```

各部分含义：

- `p`：入口探测点（probe at function entry），区别于返回探测点（`r:`）；
- `/data/tdsql_run/4001/mysql-server-8.0.24/bin/mysqld`：目标可执行文件（mysqld）路径；
- `_Z23buf_page_optimistic_getmP11buf_block_tm10Page_fetchPKcmP5mtr_t`：C++ Name Mangling 修饰后的符号（Itanium ABI：`_Z` 前缀 + 名称长度 23 + 参数类型编码，`m` = `unsigned long`，`P11buf_block_t` = `buf_block_t*`，`PKc` = `const char*`……），用于支持重载与命名空间；
- `space=+0(%si):s32 page_number=+4(%si):s32`：数据捕获规则——对 `%si` 寄存器指向的内存做偏移解引用，分别读取 `space_id` 与 `page_no`（32 位有符号整数）。

### 为什么是 %rsi 寄存器？

在 x86_64 架构的 System V ABI 调用约定中，函数的前 6 个整数/指针参数通过寄存器传递，顺序为：`%rdi`（第 1 个）、`%rsi`（第 2 个）、`%rdx`（第 3 个）、`%rcx`（第 4 个）、`%r8`（第 5 个）、`%r9`（第 6 个）。

`buf_page_optimistic_get` 的第二个参数是 `buf_block_t *block`，因此该指针存储在 `%rsi` 中（用户命令中写作 `%si`，是工具的简写/兼容写法）。

参考：<https://docs.kernel.org/trace/uprobetracer.html>

### tracing 结论

统计 uprobe 采样到的 `(space, page_no)` 分布，最终发现 **99%+ 的访问都打在同一个 page 上**——即账号表索引的根页（root page）。可以结合 `information_schema.INNODB_BUFFER_PAGE` 观察 buffer pool 中的 page 分布，交叉验证这一结论：

<https://dev.mysql.com/doc/refman/8.4/en/information-schema-innodb-buffer-page-table.html>

至此，问题链条完全闭环：

```
高并发点查 → 全部经过索引根页 → buf_page_optimistic_get 竞争同一 page 锁字
        → 自旋/睡眠/唤醒风暴 → 内核调度开销激增 → QPS 崩塌
```

## 深入分析

### 一、为什么添加字段无效？

实测"修改表结构添加字段后，最热的 page 仍是索引页"。其根本原因与**索引页的存储结构、B+ 树非叶子节点的特性、查询访问模式**密切相关：

#### 1. 索引页的本质：B+ 树非叶子节点的存储内容

InnoDB 的索引（B+ 树）由非叶子节点（内部节点）和叶子节点组成：

- **非叶子节点**：存储索引键值（如 `user_id`）和指向子节点的指针（Page No），用于快速定位数据所在的子树范围；
- **叶子节点**：存储完整的行数据（或主键值），是查询的最终目标。

本场景中，原热点 page 是索引的非叶子节点（总大小 < 10KB，约 229 条记录），存储的是索引键和子节点 Page No。此时：

- 新增字段（假设为 `new_col`）未被包含在现有索引中，因此现有索引的非叶子节点内容**完全没有变化**（仍存储原索引键和子节点指针）；
- 新增字段的数据仅存储在数据页（叶子页）中，而查询仍通过原索引（`WHERE user_id = ?`）访问数据，则原索引的非叶子节点仍是**必经之路**——树形结构决定了根节点的访问次数等于整棵树的访问次数，其热度不会下降。

#### 2. 单个 Page 的容量限制：非叶子节点的紧凑性

InnoDB 默认 Page 大小为 16KB，非叶子节点需满足 B+ 树的节点分裂规则（通常要求填充率 ≥ 50% 以避免频繁分裂）。本场景中：

- 原索引非叶子节点总大小 < 10KB，填充率较低，仍有冗余空间；
- 新增字段若未被索引使用，**不会触发索引结构任何调整**（分裂、合并、页迁移），B+ 树的形状不变，热点自然不变。

#### 3. 查询访问模式未改变：热点仍集中在原索引

若业务查询的核心条件（`WHERE user_id = ?`）未改变，且未创建包含新字段的索引，查询仍需通过原索引定位数据：

- 原索引的非叶子节点作为 B+ 树的"导航节点"，始终是查询路径中的关键节点，`buf_page_optimistic_get` 的调用仍然集中在其上；
- 新增字段只影响数据页的存储，未改变查询的"入口"（原索引），因此无法缓解原索引 page 的锁竞争。

### 二、为什么添加索引有效？

实测：新增 16 个字段并创建索引后，**QPS 提升 26%**，且 `buf_page_optimistic_get` 热点从火焰图上消失。这一现象可从三个层面解释：

#### 1. 新增索引改变了查询的执行计划

新增索引覆盖了业务的高频查询条件（如 `WHERE new_col1 = ? AND new_col2 = ?`），使查询从"原索引扫描"转变为"新索引扫描"：

- 查询无需再频繁访问原索引的非叶子节点（原热点 page），转而访问新索引的 B+ 树（不同的物理 page）；
- 关键在于：**新索引是一棵全新的 B+ 树，其根页/上层节点是完全不同的物理页**，访问压力被物理隔离到另一棵树上；
- 原索引 page 的锁竞争大幅减少，火焰图上热点消失。

#### 2. 新索引优化了页面访问模式

- **减少页分裂**：新增索引字段较短（索引总长度限制 3072 字节）且选择性好（重复值少），单页可容纳更多键值，降低分裂频率；
- **提升页内缓存利用率**：字段短、选择性高意味着非叶子节点更紧凑，单个 page 可存储更多键值和指针（对比原索引单页仅 229 条记录），减少查询时的页跳转次数，降低锁持有时间。

#### 3. 索引覆盖查询，减少回表

若新增索引为覆盖索引（包含查询所需全部字段），查询可直接从索引中获取数据，无需回表：

- 原数据页的访问压力转移到新索引的 page 上，且新索引 page 数量更多、热度更分散，避免了单一 page 的锁竞争；
- `buf_page_optimistic_get` 的调用从原索引的单个 page 分散到新索引的多个 page，单点竞争强度显著下降，性能拐点消失。

## 解决方案总结与通用思路

| 方案 | 原理 | 效果 |
| --- | --- | --- |
| 调整 threadpool 参数 | 控制活跃线程数，限制锁排队长度 | QPS 7200 → 10400（+44%），治标 |
| 新增字段 | 期望打散 page 访问 | **无效**：不改变 B+ 树形状 |
| 新增索引 | 切换执行计划，访问压力转移到新 B+ 树 | QPS +26%，热点消失，治本 |

针对"小表热点"这一通用问题，还可以考虑：

1. **自适应哈希索引（AHI）**：InnoDB 可为高频等值查询自动建立"索引键 → 页"的哈希表，跳过 B+ 树上层节点的遍历，直接减少对根页的访问（注意通过 `innodb_adaptive_hash_index_parts` 分区以避免 AHI 自身的锁竞争）；
2. **读写分离**：利用 TDSQL 只读实例将读流量分摊到多个节点，单节点内同一 page 的竞争随之下降；
3. **应用层缓存**：对变更极少的小表，可在应用侧引入本地/Redis 缓存，彻底消除数据库层的页竞争。

## 参考资料

- [Linux kernel uprobe 文档](https://docs.kernel.org/trace/uprobetracer.html)
- [MySQL INNODB_BUFFER_PAGE 表](https://dev.mysql.com/doc/refman/8.4/en/information-schema-innodb-buffer-page-table.html)
- MySQL 8.0.24 源码：`storage/innobase/include/buf0buf.h`、`storage/innobase/buf/buf0buf.cc`
