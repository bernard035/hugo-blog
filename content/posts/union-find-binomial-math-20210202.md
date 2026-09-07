---
title: "点一成零 并查集 组合数学"
date: 2021-02-02T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "并查集", "数论", "数学"]
---
前置知识：简单并查集、简单逆元、简单组合数学

本题数据量很小于是可以暴力合并，我这里是用dfs的，这样并查集就不用重复路径压缩了。

用并查集维护，新加进来的数也可以实时合并或增加集合。

答案其实就是每个集合里面的点数累乘，最后乘一个集合数量的全排列即可。

详细来说就是：

1. 把所有相邻的1合并到同一集合里
2. 用并查集可以找到一共有多少个集合，以及这些集合里有多少个数
3. 答案就是每个集合里面元素数量相乘，然后考虑集合之间也有顺序，所以乘一个集合数量的全排列
4. 新加进来的1考虑它周围能否合并，以及能否合并多个集合，如果可以，就需要维护之前的累乘：除一下之前多乘的集合数量（集合数量少了），除之前分散的集合中元素数，然后再乘上新合并的集合元素数即可。

编程使用了一些技巧比如宏函数.

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
#define pos(x, y) ((x - 1) * n + (y))
#define out(x, y) ((x) < 1 || (x) > n || (y) < 1 || (y) > n)
using namespace std;
typedef long long ll;
const int N = 500 + 7;
const int mod = 1e9 + 7;
ll qkpow(ll a, ll b) {
    ll ans = 1;
    while (b) {
        if (b & 1) ans = ans * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return ans;
}
ll getInv(ll a) { return qkpow(a, mod - 2); }  //求一个数的逆元
int sz[N * N];
int fa[N * N];
inline void init(int n) {
    for (int i = 0; i <= n; ++i) fa[i] = i, sz[i] = 1;
}
inline int Find(int x) {
    if (x != fa[x]) fa[x] = Find(fa[x]);  //路径压缩
    return fa[x];
}
void merge(int x, int y) {
    int fx = Find(x);
    int fy = Find(y);
    if (fx != fy) fa[fx] = fy, sz[fy] += sz[fx];
}
int n;
char g[N][N];
bool vis[N][N];
int dx[] = {1, -1, 0, 0};
int dy[] = {0, 0, 1, -1};
void dfs(int x, int y, int fa) {
    if (out(x, y)) return;
    vis[x][y] = 1;
    if (pos(x, y) != fa) merge(pos(x, y), fa);
    rep(i, 0, 3) {
        if (g[x + dx[i]][y + dy[i]] == '1' && !vis[x + dx[i]][y + dy[i]])
            dfs(x + dx[i], y + dy[i], fa);
    }
}
ll ans = 1;
ll cnt = 0;  //集合数量
void chk(int x, int y) {
    if (g[x][y] == '1') return;
    g[x][y] = '1';
    ++cnt;
    ans = ans * cnt % mod;
    int now = pos(x, y);
    rep(i, 0, 3) {
        if (!out(x + dx[i], y + dy[i]) && g[x + dx[i]][y + dy[i]] == '1') {
            int nxt = pos(x + dx[i], y + dy[i]);
            int f1 = Find(now);
            int f2 = Find(nxt);
            if (f1 != f2) {  //维护
                ans = ans * getInv(cnt) % mod;
                ans = ans * getInv(sz[f1]) % mod;
                ans = ans * getInv(sz[f2]) % mod;
                ans = ans * (sz[f1] + sz[f2]) % mod;
                --cnt;
                merge(f1, f2);
            }
        }
    }
}
int main() {
    sc(n);
    init(n * n);
    rep(i, 1, n) scanf(" %s", g[i] + 1);
    rep(i, 1, n) rep(j, 1, n)  //合并
        if (!vis[i][j] && g[i][j] == '1') dfs(i, j, pos(i, j));
    rep(i, 1, n) rep(j, 1, n) {  //集合内元素累乘
        int now = pos(i, j);
        if (g[i][j] == '1' && Find(now) == now)
            ++cnt, ans = (ans * sz[now]) % mod;
    }
    rep(i, 1, cnt) ans = ans * i % mod;  //全排列
    int t, u, v;
    sc(t);
    while (t--) {
        sc(u), sc(v);
        chk(++u, ++v);
        pr(ans);
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/36902cdea71a40569c7161373ccb998c），发布于 2021-02-02。
