---
title: "Yet Another Hanoi Problem"
date: 2020-06-05T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
## [Yet Another Hanoi Problem](https://ac.nowcoder.com/acm/contest/5795/N)

## 题意

无论从A到C还是从C到A都必须经过B柱，求完成n层汉诺塔的移动次数

## 思路

先考虑经典汉诺塔问题，如果汉诺塔有n层，那么需要移动$2^n-1$次。

本题要求必须经过B柱，问题就转化为，**有多少次操作是跨过了B柱的**。

如果是萌新，建议写一个最开始的汉诺塔程序，做一个模拟，在递归的输出部分稍作调整，就可以观察到$2^n-1$次里有$2^{n-1}$次是跨过B柱的，这些次数本来是只需要移动一次即可完成（eg.a->c），现在需要两次(eg.a->b->c)才可以，所以加上，结果就是$3^n-1$，也就是我们需要求出的答案。

## solution

```
T = int(input())
for i in range(T):
    n = int(input())
    print(pow(3,n,1000000007)-1)
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/528190782edc4067800c14556977b921），发布于 2020-06-05。
