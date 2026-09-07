---
title: "Color Sequence 异或前缀和 江西省赛2020"
date: 2021-01-19T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
## 题意

给定一个颜色序列，求它有多少个**颜色出现次数都是偶数**的连续子序列。

## 思路

出现次数为偶数容易联想到异或的性质：异或前缀和。

1. 由于c很小，所以可以用int作为一个01串来存储颜色是否出现过，同时也可以利用异或
2. 通过前缀和来存储len从1到n的01串的状态
3. 然后对于每个前缀和，找有多少个同值前缀（桶实现）。通过异或同值，也即切掉前缀，即可得到一个异或值为0，出现次数为偶数的子串。

## solution

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
#define sc(x) scanf("%lld", &(x))
ll a[1000005], n;
int sum[1000007];
unordered_map<int, ll> cnt;
signed main() {
    sc(n);
    for (int i = 1; i <= n; ++i) sc(a[i]);
    for (int i = 1; i <= n; ++i) sum[i] = sum[i - 1] ^ (1 << a[i]);
    ll ans = 0;
    cnt[0] = 1;
    for (int i = 1; i <= n; ++i) ans += cnt[sum[i]], ++cnt[sum[i]];
    printf("%lld\n", ans);
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/f23cb60a94b14a7ea1885d97252d202a），发布于 2021-01-19。
