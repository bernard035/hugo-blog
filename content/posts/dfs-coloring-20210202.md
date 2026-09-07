---
title: "红与蓝 DFS 染色"
date: 2021-02-02T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
1. 叶子节点之与父亲有边相连，所以叶子节点必然与父亲同色。
2. 而父亲节点已经和叶子节点同色，所以叶子节点必然与爷爷节点异色。
3. 爷爷的颜色确定后，如果爷爷还与一条边相连，那么标记爷爷的相邻点颜色也确定。对于任何一条路径，可以这样递归上去。
4. 所以统计同色（友）信息然后再跑一边DFS染色即可

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 1E5 + 10;
ll read() {
    ll a = 0;
    char b = 1, c;
    do
        if ((c = getchar()) == 45) b = -1;
    while (c < 48 || c > 57);
    do
        a = (a << 3) + (a << 1) + (c & 15);
    while ((c = getchar()) > 47 && c < 58);
    return a * b;
}
int head[N], nex[N << 1], to[N << 1];
int cnt;
int fri[N];
bitset<N> b;
void add(int u, int v) {
    nex[++cnt] = head[u];
    head[u] = cnt;
    to[cnt] = v;
}
void dfs1(int x, int f) {
    for (int i = head[x]; i; i = nex[i]) {
        int t = to[i];
        if (t == f) continue;
        dfs1(t, x);
        if (!fri[x] && !fri[t]) {
            fri[x] = t;
            fri[t] = x;
        }
    }
}
void dfs2(int x, int f) {
    for (int i = head[x]; i; i = nex[i]) {
        int t = to[i];
        if (t == f) continue;
        b[t] = t == fri[x] ? b[x] : !b[x];
        dfs2(t, x);
    }
}
int main() {
    int n = read();
    for (int i = 1; i < n; ++i) {
        int u = read(), v = read();
        add(u, v);
        add(v, u);
    }
    dfs1(1, 0);
    if (!*min_element(fri + 1, fri + 1 + n)) {
        puts("-1");
        return 0;
    }
    dfs2(1, 0);
    for (int i = 1; i <= n; ++i) putchar(b[i] ? 'R' : 'B');
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/ce53b23b0115470bb923f0cdeff1c671），发布于 2021-02-02。
