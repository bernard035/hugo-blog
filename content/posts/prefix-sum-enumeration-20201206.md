---
title: "区间异或 前缀和 优化枚举"
date: 2020-12-06T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "模拟"]
---
本题数据较水。

通过前缀和降低所需的枚举操作，将答案打表后搜索即可得到答案。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
using namespace std;
typedef long long ll;
inline ll read() {
    ll s = 0, f = 1;
    char ch;
    do {
        ch = getchar();
        if (ch == '-') f = -1;
    } while (ch < 48 || ch > 57);
    while (ch >= 48 && ch <= 57)
        s = (s << 1) + (s << 3) + (ch ^ 48), ch = getchar();
    return s * f;
}
ll n, m, a[3005], x, s[3005];
ll res[3005];  // res[i]表示i个数能够凑到的最大值
int main() {
    n = read(), m = read();
    for (int i = 1; i <= n; ++i) a[i] = read();
    s[1] = a[1];  //异或前缀和
    for (int i = 1; i <= n; ++i) s[i] = s[i - 1] ^ a[i];
    for (int i = 1; i <= n; ++i)  //枚举每一种取值
        for (int len = 1; i + len - 1 <= n; ++len)
            res[len] = max(res[len], s[len + i - 1] ^ s[i - 1]);
    for (int i = 2; i <= n; ++i) res[i] = max(res[i - 1], res[i]);
    while (m--) {
        x = read();
        int t = lower_bound(res + 1, res + n + 1, x) - res;
        pr(t > n ? -1LL : t);
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/c8dc3886896447db99153f6606b586d4），发布于 2020-12-06。
