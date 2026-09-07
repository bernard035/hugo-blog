---
title: "线性基 Linear Base"
date: 2021-05-01T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/算法笔记"]
tags: ["学习笔记"]
---
## 性质

1. 线性基的元素能通过**相互异或**，**得到原序列的元素的所有相互异或得到的值**。也可以得到原序列的任意一个数。- 线性基是满足性质1的最小集合。- 线性基没有异或和为 0 的子集。- 线性基中每个元素的异或方案唯一，也就是说，线性基中不同的异或组合异或出的数都是不一样的。- 线性基中每个元素的二进制最高位互不相同。

```
ll d[64];
void add(ll x) {
    for (int i = 60; ~i; --i) {
        if (x & (1ll << i)) {  // 如果x此位有
            if (d[i])          // 如果本位此前已存值
                x ^= d[i];     // 已有的消除
            else {
                d[i] = x;  // 直接存储
                break;
            }
        }
    }
}
```

## 求最大值

求序列中的数所能异或出来的最大值

```
ll solve() {
    ll ans = 0;
    for (int i = 60; ~i; --i) {
        if (ans ^ d[i] > ans) ans ^= d[i];
    }
    return ans;
}
```

## 求最小值

判断有没有add失败，如果有，就是0，否则是d[0]

## 求第k小的值

```
ll d[64], n, tot;
void add(ll x) {
    for (int i = 60; ~i; --i) {
        if (x & (1ll << i)) {  // 如果x此位有
            if (d[i])          // 如果本位此前已存值
                x ^= d[i];     // 已有的消除
            else {
                d[i] = x;  // 直接存储
                break;
            }
        }
    }
}
void work() {  //处理线性基
    for (int i = 1; i <= 60; i++)
        for (int j = 1; j <= i; j++)
            if (d[i] & (1ll << (j - 1))) d[i] ^= d[j - 1];
}
ll kTh(ll k) {
    if (k == 1 && tot < n)
        return 0;  //特判一下，假如k=1，并且原来的序列可以异或出0，就要返回0，tot表示线性基中的元素个数，n表示序列长度
    if (tot < n) k--;  //类似上面，去掉0的情况，因为线性基中只能异或出不为0的解
    work();
    ll ans = 0;
    for (int i = 0; i <= 60; i++)
        if (d[i] != 0) {
            if (k % 2 == 1) ans ^= d[i];
            k /= 2;
        }
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/66ae393891d449b1a8377d2fe5d56679），发布于 2021-05-01。
