---
title: "贪心 思维 B. Combinatorics Homework Edu CF Round 114 (Div. 2)"
date: 2021-09-24T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "Codeforces", "贪心"]
---
## 题意

有a个字符A，b个字符B，c个字符C

需要构造恰好m个相邻位置相同的pair的字符串

exactly m pairs of adjacent equal letters (exactly m such positions i that the i-th letter is equal to the (i+1)-th one).

## 思路

贪心考虑上下界即可。其实就是连续的。

## solution

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
signed main() {
    ll T;
    sc(T);
    ll a[3], m;
    while (T--) {
        rep(i, 0, 2) sc(a[i]);
        sc(m);
        sort(a, a + 3);
        ll L = max(0LL, a[2] - a[1] - a[0] - 1);
        // 最短的排列方式就是   ABCABCABABAAAA 
        // 但是需要注意的是可以 AACABCABABABAA
        // 所以需要再 - 1
        ll R = a[0] + a[1] + a[2] - 3;
        // 最长的排列方式就是连续排列
        if (m >= L && m <= R)
            puts("YES");
        else
            puts("NO");
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/1c52d84abcb546b082651275e51fe00f），发布于 2021-09-24。
