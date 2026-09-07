---
title: "Berland Crossword 思维题"
date: 2021-03-03T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
![图片说明](/images/d34bd5c09bd554f9.png)

给定矩阵大小，最顶上一行有多少黑格，最右一列有多少黑格，最下一行有多少黑格，最左一列有多少黑格。

问是否存在这样的矩阵。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define nxt (i + 1) % 4
#define lst (i + 3) % 4
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
bool solve() {
    int n;
    sc(n);
    int a[4] = {0}, d[4] = {0};
    rep(i, 0, 3) {
        sc(a[i]);
        if (a[i] == n) ++d[nxt], ++d[lst];
    }
    rep(i, 0, 3) if (a[i] == n - 1) {
        if (a[nxt] - a[lst] >= d[nxt] - d[lst])  //
            ++d[nxt];
        else
            ++d[lst];
    }
    rep(i, 0, 3) if (a[i] < d[i]) return 0;
    return 1;
}
int main() {
    int T;
    sc(T);
    while (T--) puts(solve() ? "YES" : "NO");
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/d27aa95eb30e4cfd9027102eeeaed990），发布于 2021-03-03。
