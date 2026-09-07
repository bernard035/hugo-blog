---
title: "激光炸弹 二维前缀和"
date: 2020-05-18T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
二维前缀和，思路是建一个二维数组表示从$[1,1]$炸到$[i,j]$所能收获的value之和。

然后就一个一个比过去就好了。

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 5e3 + 7;
const ll mod = 1e9 + 7;
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
int a[N][N];
int main() {
    int n = read(), r = read(), x, y, v, ans = 0;
    for (int i = 0; i < n; ++i) x = read(), y = read(), v = read(), a[x+1][y+1] = v;
    for (int i = 1; i < N; i++)
        for (int j = 1; j < N; j++)
            a[i][j] += a[i - 1][j] + a[i][j - 1] - a[i - 1][j - 1];
    for (int i = r; i < N; i++)
        for (int j = r; j < N; j++)
            ans = max(ans, a[i][j] - a[i][j - r] - a[i - r][j] + a[i - r][j - r]);
    printf("%d",ans);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/ec9ee46406cf4e558313dd7eee743da4），发布于 2020-05-18。
