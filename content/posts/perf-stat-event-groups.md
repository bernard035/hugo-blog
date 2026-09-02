---
title: "perf stat 硬件事件组编排最佳实践"
date: 2026-09-03T17:00:00+08:00
draft: false
tags: ["perf", "Linux", "PMU", "性能分析"]
categories: ["性能工程"]
showToc: true
TocOpen: false
---

# perf stat 硬件事件组编排最佳实践

> `perf stat` 是 CPU 性能分析的第一入口，但事件编排不当会导致数据被多路复用（multiplexing）缩放污染，甚至得到自相矛盾的结论。本文从 PMU 硬件约束出发，系统梳理事件组（event groups）的编排原则、常见陷阱与推荐组合。

## 一、为什么事件编排很重要

每个 CPU 核心的 PMU（Performance Monitoring Unit）只有**有限的硬件计数器**，而 `perf stat` 需要同时监控多个性能事件。当事件数超过计数器数量时，perf 会自动启用**计数器多路复用**：事件分时轮询计数器，测量值按运行时间比例缩放。

缩放公式：

```
缩放值 = 原始计数 × (time_enabled / time_running)
```

问题在于：**缩放引入误差**，且两个分别缩放的事件（如 instructions 与 cycles）不再来自同一时间窗口，据此计算的 IPC 可能失真。事件组的本质目的就是：**让语义相关的多个事件在同一时间窗口、同一 PMU 上被原子地采集**。

## 二、Event Group 基础

用 `{}` 把事件编为一组，表示这些事件应尽可能同时调度：

```bash
perf stat -e '{instructions,cycles,cache-misses}' ./my_program
```

两个进阶技巧：

1. **强组（strong group）**：默认组是"弱约束"——调度器尽力而为，必要时仍会拆分。追加 `:S` 修饰符变成强组，**无法整组调度时直接报错**而不是静默降级，适合对数据一致性要求高的场景：

   ```bash
   perf stat -e '{instructions,cycles}:S' ./my_program
   ```

2. **组级修饰符**：修饰符作用于整组，如 `{cycles,instructions}:u` 表示只统计用户态，避免逐个事件书写。

## 三、PMU 计数器资源约束

### 3.1 计数器数量

以主流 x86 平台为例：

- **固定计数器（fixed counters）**：3 个，专用于 `instructions`、`cycles`、`ref-cycles`，不参与通用计数器分配（Ice Lake 及之后还有一个 `slots` 固定计数器，服务于 topdown 分析）；
- **通用计数器（generic counters）**：通常 4~8 个，供任意通用事件竞争。

关键限制：

- **超线程**：两个兄弟逻辑核**共享**同一组物理计数器，HT 激活时每线程可用通用计数器大约减半；
- **NMI watchdog**：默认会长期占用一个计数器，服务器上做精细测量建议通过内核参数 `nmi_watchdog=0` 释放；
- **经验法则**：每组 3~4 个相关事件，尽量避免单组超过 6 个硬件事件。

### 3.2 判断是否发生了 multiplexing

- `perf stat` 输出中每个事件附带 `time_running`/`time_enabled` 比值，或标注 `<multiplexed>`；
- 若 `time_running << time_enabled`，说明该事件实际只运行了小部分时间，数据可信度低；
- 对策：削减组内事件数、拆分成多次运行、拉长测量窗口。

## 四、分组语义学：把相关事件放同一组

**同一组内的事件应服务于同一个分析目标**，这样它们的比值才有意义：

```bash
# CPU 效率：IPC（理想值与架构相关，通常 1~4）
perf stat -e '{instructions,cycles}' ./app

# 缓存行为：命中率与缺失率
perf stat -e '{cache-references,cache-misses}' ./app
perf stat -e '{L1-dcache-loads,L1-dcache-load-misses}' ./app

# 分支行为：预测质量
perf stat -e '{branch-instructions,branch-misses}' ./app
```

### 跨 PMU 混合是常见错误

不同事件可能挂在不同 PMU 上（core PMU、uncore PMU、软件事件、tracepoint），跨 PMU 事件无法真正"同时"运行：

```bash
# 反例：cycles(core) + page-faults(software) + mem-loads(intel uncore/特殊PMU)
perf stat -e '{cycles,page-faults,mem-loads}' ./app
```

正确做法是按 PMU 拆分运行，或先用 `perf list` 确认事件的归属与兼容性：

```bash
perf list hardware     # 硬件事件
perf list software     # 软件事件
perf list cache        # 模糊匹配
```

## 五、事件修饰符与采样开销

| 修饰符 | 含义 | 使用建议 |
| --- | --- | --- |
| `:u` / `:k` / `:h` | 仅用户态 / 内核态 / hypervisor | 不区分态会混淆数据来源，统计类测量建议显式指定 |
| `:p` / `:pp` / `:ppp` | 精确采样级别（对应 PEBS / IBS 硬件支持） | `stat` 场景不要滥用 |

`:pp` 以上级别会启用基于 PEBS（Intel）/ IBS（AMD）的精确采样，频繁中断程序、开销显著：

```bash
# 危险：stat 只需要计数，精确采样纯属浪费
perf stat -e '{cycles,instructions}:ppp' ./app
```

**原则：`perf stat` 做计数时用普通事件；`:p` 系列留给 `perf record` 采样定位热点时使用。**

## 六、作用域与聚合控制

高精度测量还需要控制"在哪里测、怎么聚合"：

```bash
# 只监控 CPU 0，按核分别显示（不聚合）
perf stat -C 0 -A -e '{cycles,instructions}' ./app

# 每核 / 每 socket 视角，分析 NUMA 与共享资源
perf stat --per-core -e cycles,instructions ./app
perf stat --per-socket -e '{cache-misses,instructions}' ./app

# 时间窗口采样，观察随时间的变化（如节流、干扰）
perf stat -I 1000 -e '{cycles,instructions}' -a sleep 10

# 重复运行取平均，抑制瞬时抖动
perf stat -r 5 -e '{instructions,cycles}' ./app
```

要点：

- `-C` 限定 CPU、`-a` 统计全系统、`-A` 禁止跨核聚合——多核系统上聚合值会掩盖核间失衡；
- `-I`（interval）配合 `-a sleep N` 是观测系统级干扰（如邻居负载、频率回落）的利器；
- 短命程序计数不可靠，务必用 `-r N` 重复或拉长负载。

## 七、架构差异：事件名不可移植

不同架构（Intel/AMD/ARM/RISC-V）的 PMU 事件名不同：

- **通用事件名**（`cycles`、`instructions`、`cache-misses`、`branch-misses`）跨架构可用，是脚本化的首选；
- **架构专属事件**（如 Intel 的 `idq_uops_not_delivered.core`、ARM 的 `mem_access` 类事件）需用 `perf list` 查询等效名称；
- 需要 CPU 编号级精确事件时，可用 raw 事件语法 `rNNN`（如 `r01D0` 对应 Intel 的 UOPS_RETIRED），但这要求对照 SDM/架构手册，可移植性最差。

## 八、Topdown 分析与推荐事件组

### 8.1 TMA：从四大瓶颈分类开始

现代性能分析建议先用 **Topdown Microarchitecture Analysis（TMA）** 把 CPU 时间分类到四大桶，再针对性下钻：

```
Pipeline Slots
├── Retiring            有效工作
├── Bad Speculation     投机执行被浪费（分支预测失败等）
├── Frontend Bound      取指/译码供不上
└── Backend Bound       执行资源/访存供不上（可继续下钻 Memory Bound / Core Bound）
```

在较新的 Intel 平台（Ice Lake+）上，`slots` 是固定计数器，topdown 指标可由硬件直接度量；一步到位的方法：

```bash
perf stat -M TopdownL1 ./app     # 或较新版本 perf stat 默认输出 tma_* 指标
```

### 8.2 推荐事件组速查

| 分析目标 | 推荐事件组 |
| --- | --- |
| CPU 效率 | `{instructions,cycles}` → IPC |
| 缓存效率 | `{cache-references,cache-misses}` 或 `{L1-dcache-loads,L1-dcache-load-misses}` |
| 内存访问 | `{mem-loads,mem-stores}`（Intel） |
| 分支预测 | `{branch-instructions,branch-misses}` |
| 前端瓶颈 | `{idq_uops_not_delivered.core,cycles}`（Intel，TMA 下钻） |
| TMA 一级分类 | `-M TopdownL1` |

发现异常后，用 `perf record` 下钻到函数级热点：

```bash
perf record -e cycles -c 10000 ./app
perf report
```

## 九、常见错误与最佳实践清单

| 常见错误 | 后果 |
| --- | --- |
| 单组事件过多 | multiplexing，数据被缩放、失真 |
| 跨 PMU 混合分组 | 无法同时调度，被迫拆分或报错 |
| 滥用 `:ppp` 精确采样 | 程序变慢，测量干扰被测对象 |
| 不区分用户态/内核态 | 数据来源混淆，结论歧义 |
| 忽略架构事件差异 | 事件不存在或语义不等价 |
| 测量窗口太短 | 计数不可靠，抖动主导 |
| 忽略 HT 与 NMI watchdog | 可用计数器比预期少，莫名 multiplexing |

**最佳实践总结：**

1. **精简**：每组 3~4 个语义相关的硬件事件；
2. **一致**：同一组服务于同一分析目标，比值才有意义；
3. **不跨 PMU**：core/uncore/software 事件分开测；
4. **先验证**：`perf list` 确认事件存在与归属；
5. **控开销**：stat 用普通事件，精确采样留给 record；
6. **可重复**：`-r N` + 足够长的测量窗口，检查 multiplexing 比例；
7. **先 topdown 后下钻**：TMA 分类 → 定位瓶颈桶 → 事件组/火焰图细查。

## 参考资料

- [perf Tutorial（kernel.org）](https://perfwiki.github.io/main/tutorial/)
- [perf-stat 手册页](https://man7.org/linux/man-pages/man1/perf-stat.1.html)
- Ahmad Yasin, *A Top-Down Method for Performance Analysis and Counters Architecture*（ISPASS 2014）
- Intel SDM Vol.3 Chapter "Performance Monitoring"
