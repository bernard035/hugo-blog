---
title: "前缀和  中位数图"
date: 2020-09-12T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
1. 先对数据进行处理：大于b的改成1，小于的改成-1，等于的改成0
2. 找到需要定的中位数b的位置
3. 从这个位置从左往右扫一遍，统计当前值的出现次数
4. 再从右往左扫一遍
5. 最后的答案就是左边为零的数量+右边为零的数量+单独的b，再加上左边加右边能凑到零的数量，即互为相反数的LR数组中的积。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", x)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
ll a[N];
int L[N << 1], R[N << 1];
int main() {
    ll n, b, p;
    sc(n), sc(b);
    for (int i = 1; i <= n; ++i) {
        sc(a[i]);
        if (a[i] > b) a[i] = 1;
        else if (a[i] < b) a[i] = -1;
        else a[i] = 0, p = i;
    }
    for (int i = p - 1, res = 0; i >= 1; --i) {
        res += a[i];
        ++L[res + n];
    }
    for (int i = p + 1, res = 0; i <= n; ++i) {
        res += a[i];
        ++R[res + n];
    }
    ll ans = L[n] + R[n] + 1;
    for (int i = 0; i <= (n << 1); ++i) ans += L[n + n - i] * R[i];
    pr(ans);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/a30469bad7924a5280d20d49ed94ac09），发布于 2020-09-12。
