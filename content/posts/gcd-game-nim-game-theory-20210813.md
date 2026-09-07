---
title: "GCD GAME 签到 nim博弈"
date: 2021-08-13T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "博弈"]
---
考虑nim博弈：n堆石子，没人每次取任意一堆的任意数量的石子，问谁能赢，对于这样的问题，我们把所有堆的石子数异或起来，只要不为0，先手必胜。

本题就是一个小小的转化：每堆石子数就是其因数个数。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &x)
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e7 + 7;
const int mod = 1e9 + 7;
bitset<N> isPrime;
vector<int> primes;
int fac[N];
void sieve(int n) {
    isPrime.set();
    isPrime[0] = isPrime[1] = 0;
    for (int i = 2; i <= n; ++i) {
        if (isPrime[i]) primes.emplace_back(i), fac[i] = 1;
        for (int p : primes) {
            if (i * p > n) break;
            fac[i * p] = fac[i] + 1;
            isPrime[i * p] = 0;
            if (i % p == 0) break;
        }
    }
}
signed main() {
    sieve(N);
    int T, n, x;
    sc(T);
    while (T--) {
        sc(n);
        int ans = 0;
        rep(i, 1, n) {
            sc(x);
            ans ^= fac[x];
            pr(fac[x]);
        }
        puts(ans ? "Alice" : "Bob");
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/f74b3a5fccb44c83881ac51b7c7bff6b），发布于 2021-08-13。
