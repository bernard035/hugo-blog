---
title: "WhiteAlgorithm 代码"
date: 2020-08-08T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/算法笔记"]
tags: ["学习笔记"]
---
## Prim

```
const int maxn = 5e2 + 7;

int n;              //点的个数
int dis[maxn];      //标记当前到某点的最短距离
bool vis[maxn];     //标记某点是否已经在树里
int e[maxn][maxn];  //邻接矩阵存图

//因为prim算法时间复杂度O(n*n)，广泛用于稠密图，所以用邻接矩阵是一个合适的选择

int Prim() {
    int ans = 0;
    for (int i = 0; i <= n; ++i) dis[i] = 2e9, vis[i] = 0;  //初始化
    dis[1] = 0;  // prim算法任选起点，于是可以选择1为起点
    for (int i = 1; i <= n; ++i) {
        int u = 0;  //注意dis[0]已经初始化为无穷大了，不用管了
        for (int j = 1; j <= n; ++j)
            if (!vis[j] && dis[j] < dis[u]) u = j;
        vis[u] = 1;
        ans += dis[u];  //更新答案
        for (int j = 1; j <= n; ++j)
            if (!vis[j] && dis[j] > e[u][j])  //拿u点来更新新拓展的边
                dis[j] = e[u][j];
    }
    return ans;  //生成树总长度
}
```

## Kruskal

```
#include <algorithm>
const int maxn = 500 + 7;
const int maxm = 500 * 500 + 7;
int n;         //点的数量
int m;         //边的数量
struct edge {  //边
    int u;     //起点
    int v;     //终点
    int w;     //权重
} edges[maxm];
// Kruskal算法的前置知识是并查集，来判断已选边是否成环
//设点数量为n，边数量为m，Kruskal算法的时间复杂度为O(n)+O(m*logm)
int bin[maxn];  //每个节点对应的根节点的信息
void unit() {   //初始化
    for (int i = 0; i <= m; i++) bin[i] = i;
}
void merge(int x, int y) {  //并
    int fx = Find(x);
    int fy = Find(y);
    if (fx != fy) bin[fx] = fy;
}
int Find(int x) {  //查
    int px = x;
    while (px != bin[px]) px = bin[px];
    return px;
}
bool cmp(edge a, edge b) {  //将边按照权重从小到大排序
    return a.w < b.w;
}
int Kruskal() {
    int sumweight = 0;  //生成树的权值
    int num = 0;        //已选用的边的数目
    int u, v;           //选用边的两个顶点
    unit();             //初始化 parent[]数组
    std::sort(edges, edges + m);
    for (int i = 0; i < m; i++) {
        u = edges[i].u;
        v = edges[i].v;
        if (Find(u) != Find(v)) {
            //如果满足不成环，就加入生成树
            sumweight += edges[i].w;
            num++;
            merge(u, v);
        }
        if (num == n - 1) break;  //边的数量达到n-1条时，生成树构建完成
    }
    return sumweight;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/a040bf3569e4437e9e6e83e3301b0c4c），发布于 2020-08-08。
