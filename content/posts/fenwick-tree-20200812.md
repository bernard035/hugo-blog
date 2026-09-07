---
title: "树状数组"
date: 2020-08-12T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/算法笔记"]
tags: ["学习笔记", "树状数组"]
---
![图片说明](/images/b79321e282db5671.png)

## 代码

```
#define lowbit(x) ((x) & (-x))
ll tree[N];
inline void update(int i, ll x) {
    for (int pos = i; pos < N; pos += lowbit(pos)) tree[pos] += x;
}
inline ll query(int n) {
    ll ans = 0;
    for (int pos = n; pos; pos -= lowbit(pos)) ans += tree[pos];
    return ans;
}
inline ll query(int a, int b) { return query(b) - query(a - 1); }
```

## What

树状数组支持：

- **单点修改**：更改数组中一个元素的值- **区间查询**：查询一个区间内所有元素的和

两种操作的时间复杂度均为$O(log_2n)$，是**暴力**（$O(1)$+$O(n)$）和**前缀和**（$O(n)$+$O(1)$）的折中。

可以通过引入差分数组的方式完成对区间修改，单点查询的支持。

![图片说明](/images/c42c1f31aac69165.png)

## Why

![图片说明](/images/041fc797510e76bb.png)

![图片说明](/images/6522f57f8624f90e.png)

![ ](/images/fd81fbae68ec40cb.png)

## How

### 逆序数

（待填）
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/f0c54b8f98084736a024816f5696c007），发布于 2020-08-12。
