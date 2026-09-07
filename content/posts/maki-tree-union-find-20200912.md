---
title: "并查集 maki和tree"
date: 2020-09-12T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "并查集"]
---
1. 使用并查集合并白色连通块
2. 从每一个黑色节点出发，查询黑色节点的每个分支的白色节点数量，再加上彼此相乘即为结果
3. 因为回头走了，每个节点都被算了两遍，所以需要`cnt/2`

```
#include <bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 7;
typedef long long ll;
int head[maxn], to[maxn << 1], nex[maxn << 1], tot;
char s[maxn];
int fa[maxn], num[maxn], n;
ll ans;
void add(int x, int y) {
    to[tot] = y;
    nex[tot] = head[x];
    head[x] = tot++;
}
int find(int x) { return x == fa[x] ? x : fa[x] = find(fa[x]); }
void f(int x) {
    vector<int> tmp;
    int sum = 0;
    for (int i = head[x]; ~i; i = nex[i]) {
        int v = to[i];
        if (s[v] == 'W') {
            int fx = find(v);
            tmp.push_back(num[fx]);
            sum += num[fx];
            ans += num[fx];
        }
    }
    ll cnt = 0;
    for (int i = 0; i < tmp.size(); i++) cnt += (ll)tmp[i] * (sum - tmp[i]);
    ans += cnt / 2;
}
int main() {
    memset(head, -1, sizeof(head));
    scanf("%d", &n);
    scanf("%s", s + 1);
    for (int i = 1; i <= n; i++) fa[i] = i, num[i] = 1;
    for (int i = 1; i <= n - 1; i++) {
        int a, b;
        scanf("%d%d", &a, &b);
        if (s[a] == 'W' && s[b] == 'W') {
            int fx = find(a), fy = find(b);
            if (fx != fy) {
                fa[fx] = fy;
                num[fy] += num[fx];
            }
        }
        add(a, b);
        add(b, a);
    }
    for (int i = 1; i <= n; i++) {
        if (s[i] == 'B') f(i);
    }
    printf("%lld\n", ans);
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/2b3c4be68f0e43aa8eb95fd371e7ac0b），发布于 2020-09-12。
