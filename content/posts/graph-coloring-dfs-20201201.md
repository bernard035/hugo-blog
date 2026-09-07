---
title: "Graph Coloring I DFS"
date: 2020-12-01T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
本题其实考察了一个基础知识：

> 可以将图的结点用两种颜色染色，满足相邻点不同色的图，称为二分图。而在不满足二分图构成条件的图里，一定可以找到一个简单奇环。

在明确这一点的基础上，就可以使用dfs对图结构进行染色。在dfs的过程中，用栈保存点结构的遍历信息，从而检索出现奇数环的情况。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d ", x)
using namespace std;
typedef long long ll;
const int N = 5e5 + 7;
const ll mod = 1e9 + 7;
int to[N << 1], nxt[N << 1], head[N], tot;
inline void add(int u, int v) {
    to[++tot] = v;
    nxt[tot] = head[u];
    head[u] = tot;
}
int n, m;
bool color[N], err;
int st[N], pos[N], p;
void dfs(int x, int fa, bool c) {
    if (!err && pos[x] && pos[x] < p && (pos[x] - p) % 2 == 0) {
        err = 1;
        printf("%d\n", p - pos[x] + 1);
        for (int i = pos[x]; i <= p; ++i) printf("%d ", st[i]);
    }
    if (err || pos[x]) return;
    st[++p] = x, pos[x] = p;
    color[x] = c;
    for (int i = head[x]; i; i = nxt[i])
        if (to[i] != fa) dfs(to[i], x, !c);
    --p;
}
int main() {
    sc(n), sc(m);
    for (int i = 0, u, v; i < m; ++i) {
        sc(u), sc(v);
        add(u, v), add(v, u);
    }
    dfs(1, 0, 0);
    if (!err) {
        puts("0");
        for (int i = 1; i <= n; ++i) printf("%d ", color[i]);
    }
    return 0;
}
```

附上注释

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d ", x)
using namespace std;
typedef long long ll;
const int N = 5e5 + 7;
const ll mod = 1e9 + 7;
int to[N << 1], nxt[N << 1], head[N], tot;
inline void add(int u, int v) {
    to[++tot] = v;
    nxt[tot] = head[u];
    head[u] = tot;
}
int n, m;
bool color[N], err;
int st[N], pos[N], p;
void dfs(int x, int fa, bool c) {
    if (!err && pos[x] && pos[x] < p && (pos[x] - p) % 2 == 0) {  //出现奇环
        //由于上述的等价性，奇数环的判定条件 
        //(pos[x] - p) % 2 == 0可换成color[x]!=c
        err = 1;
        printf("%d\n", p - pos[x] + 1);
        for (int i = pos[x]; i <= p; ++i) printf("%d ", st[i]);
    }
    if (err || pos[x]) return;  //如果出现奇环或者此节点已经访问过
    st[++p] = x, pos[x] = p;    //入栈 用桶存栈中下标
    color[x] = c;               //染色
    for (int i = head[x]; i; i = nxt[i])
        if (to[i] != fa) dfs(to[i], x, !c);
    --p;
}
int main() {
    sc(n), sc(m);
    for (int i = 0, u, v; i < m; ++i) {
        sc(u), sc(v);
        add(u, v), add(v, u);
    }
    dfs(1, 0, 0);
    if (!err) {
        puts("0");
        for (int i = 1; i <= n; ++i) printf("%d ", color[i]);
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/b67bb46bbe5b4b30aecfdf736ee4cad9），发布于 2020-12-01。
