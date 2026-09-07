---
title: "几种最短路的简单表述"
date: 2021-04-23T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/算法笔记"]
tags: ["学习笔记", "图论"]
---
# SPFA

思路：

1. 初始化dis[]为最大值，dis[x]表示起点到x点的最短路长度
2. 建立一个queue，一开始只有起点
3. 弹出队头，对于队头节点的每个相邻点，如果能优化当前的路径，就入队，并且优化
4. 重复3直到队空

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 3e3 + 7;
const int mod = 1e9 + 7;
int a[N][N];
const int n = 2021;
void build() {
    rep(i, 1, n) rep(j, 1, n) {
        if (abs(i - j) <= 21) a[i][j] = a[j][i] = i / __gcd(i, j) * j;
    }
}
ll dis[N];
bool vis[N];
void spfa(int ori) {
    fill(dis, dis + n + 1, LLONG_MAX);
    dis[ori] = 0;
    queue<int> q;
    vis[ori] = 1;
    q.push(1);
    while (!q.empty()) {
        int x = q.front();
        q.pop();
        vis[x] = 0;
        rep(i, 1, n) if (x != i && abs(x - i) <= 21) {
            if (dis[x] + a[x][i] < dis[i]) {
                dis[i] = dis[x] + a[x][i];
                if (!vis[i]) {
                    vis[i] = 1;
                    q.push(i);
                }
            }
        }
    }
}
int main() {
    build();
    spfa(1);
    pr(dis[n]);
    return 0;
}
```

## dij

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 2005;
const int mod = 1e9 + 7;
int a[N][N];
ll dis[N][N];
bool vis[N];
vector<int> G[N];
int n, m, c;
struct node {
    ll val;
    int id;
    bool operator<(const node& o) const { return val > o.val; }
};
void dij(int ori) {
    ll* d = dis[ori];
    fill(d + 1, d + n + 1, INT_MAX);
    memset(vis, 0, sizeof vis);
    d[ori] = 0;
    priority_queue<node> pq;
    pq.push({0, ori});
    while (!pq.empty()) {
        int x = pq.top().id;
        pq.pop();
        if (!vis[x]) {
            vis[x] = 1;
            for (int i : G[x]) {
                if (d[x] + a[x][i] < d[i]) {
                    d[i] = d[x] + a[x][i];
                    pq.push({d[i], i});
                }
            }
        }
    }
}
int main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    cin >> n >> m >> c;
    int u, v, w, minw = INT_MAX;
    rep(i, 1, m) {
        cin >> u >> v >> w;
        minw = min(minw, w);
        if (!a[u][v]) {
            a[u][v] = w;
            G[u].push_back(v);
        } else {
            a[u][v] = min(a[u][v], w);
        }
    }
    rep(i, 1, n) dij(i);
    ll minc = LLONG_MAX;
    rep(i, 1, n) rep(j, i + 1, n) minc = min(minc, dis[i][j] + dis[j][i]);
    if (minc <= c)
        cout << 2;
    else if (minw <= c)
        cout << 1;
    else
        cout << 0;
    return 0;
}
```

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 3e3 + 7;
const int mod = 1e9 + 7;
int a[N][N];
const int n = 2021;
void build() {
    rep(i, 1, n) rep(j, 1, n) {
        if (abs(i - j) <= 21) a[i][j] = a[j][i] = i / __gcd(i, j) * j;
    }
}
ll dis[N];
bool vis[N];
void dij(int ori) {
    fill(dis, dis + n + 1, LLONG_MAX);
    dis[ori] = 0;
    map<ll, int> mp;
    vis[ori] = 1;
    mp[0] = 1;
    while (!mp.empty()) {
        int x = (*mp.begin()).second;
        mp.erase(mp.begin());
        vis[x] = 0;
        rep(i, 1, n) if (x != i && abs(x - i) <= 21) {
            if (dis[x] + a[x][i] < dis[i]) {
                dis[i] = dis[x] + a[x][i];
                if (!vis[i]) {
                    vis[i] = 1;
                    mp[dis[i]] = i;
                }
            }
        }
    }
}
int main() {
    build();
    dij(1);
    pr(dis[n]);
    return 0;
}
```

## floyd

```
void floyd(int **g) {
    int s = 1;
    rep(i, 1, n) rep(j, 1, n) {
        if (g[i][j]) {
            g[i][j] = INT_MAX;
        }
    }
    rep(k, 1, n) rep(i, 1, n) {
        if (a[i][k] != INT_MAX) {
            rep(j, 1, n) g[i][j] = min(g[i][j], g[i][k] + g[k][j]);
        }
    }
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/4deaccc07bc3467d8218d85d2a225460），发布于 2021-04-23。
