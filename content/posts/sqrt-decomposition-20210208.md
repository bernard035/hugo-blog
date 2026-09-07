---
title: "牛牛与整除分块"
date: 2021-02-08T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
$N=10$时$S={10,5,3,2,1}$

$$\large{S=\{\frac{N}{1},\frac{N}{2},\frac{N}{3},\frac{N}{4},\dots ,\frac{N}{\lfloor \sqrt{N} \rfloor },\frac{N}{\frac{N}{\lfloor \sqrt{N} \rfloor }}（特判）,\frac{N}{\frac{N}{\lfloor \sqrt{N} \rfloor -1}},\frac{N}{\frac{N}{\lfloor \sqrt{N} \rfloor -2}},\frac{N}{\frac{N}{\lfloor \sqrt{N} \rfloor -3}},\dots \frac{N}{\frac{N}{1}},\}}$$

化简可得

$$\large{S=\{\frac{N}{1},\frac{N}{2},\frac{N}{3},\frac{N}{4},\dots ,\frac{N}{\lfloor \sqrt{N} \rfloor },{\lfloor \sqrt{N} \rfloor }（特判）,\lfloor \sqrt{N} \rfloor -1,\lfloor \sqrt{N} \rfloor -2,\lfloor \sqrt{N} \rfloor -3,\dots 1\}}$$

于是以$\sqrt{n}$为界，在左边或者右边找对应的位置即可。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
int main() {
    ll T, n, x;
    sc(T);
    while (T--) {
        sc(n), sc(x);
        ll a = sqrt(n);
        if (x <= a)
            pr(x);
        else {
            ll l = n / a;
            ll ans = 0;
            if (a != l) ++ans;
            ll now = n / x;
            ans += a - now + a;
            pr(ans);
        }
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/da790a798ad74955bb8643eece7be19f），发布于 2021-02-08。
