---
title: "Mine Sweeper II 贪心 思维"
date: 2021-01-27T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "贪心"]
---
## 题意

给两个n\*m的扫雷图，问能不能至多反转$\lfloor n*m \rfloor$ 个格子，把图B的的空白区权值和变成和图A相同。

## 思路

本题是一道CF式的思维题。比赛的时候没做出来，遗憾。

既然是CF式的，样例必然是误导性的。

其实就是把B变成A或者A的反图即可。

下面证明A和A的反图权值和相同：

1. 雷对权值的贡献是雷的八个方向上一共有多少个空白区域
2. 空白区域对权值的贡献是八个方向上有多少个雷

由于至多调整半数格子，所以必定可以完成调整，只是判断一下是调整成A还是A的反图即可。

## Solution

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 1e3 + 7;
char a[N][N], b[N][N];
int main() {
    int n, m;
    sc(n), sc(m);
    rep(i, 1, n) scanf("%s", a[i] + 1);
    rep(i, 1, n) scanf("%s", b[i] + 1);
    int cnt = 0;
    rep(i, 1, n) rep(j, 1, m) if (a[i][j] != b[i][j]) ++cnt;
    if (cnt <= n * m / 2)
        rep(i, 1, n) puts(a[i] + 1);
    else
        rep(i, 1, n) {
            rep(j, 1, m) putchar(a[i][j] == 'X' ? '.' : 'X');
            putchar(10);
        }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/207df541a25c447882a3af2678961634），发布于 2021-01-27。
