---
title: "旋转跳跃 - 并查集"
date: 2020-07-16T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "并查集"]
---
一开始我认为本题是最短路，采用了BFS实现，只能过47%的案例。

本题的最简思路是并查集。

```
class Solution {
   public:
    int fa[100010], a[100010];
    vector<int> vt[100010];
    int find(int rt) { return fa[rt] == rt ? rt : fa[rt] = find(fa[rt]); }
    void Union(int x, int y) { fa[find(x)] = find(y); }
    vector<int> solve(int n, int m, vector<int>& perm, vector<Point>& Pair) {
        for (int i = 1; i <= n; i++) a[i] = perm[i - 1], fa[i] = i;
        for (auto i : Pair) Union(i.x, i.y);
        for (int i = 1; i <= n; i++) vt[find(i)].push_back(i);
        for (int i = 1; i <= n; i++) {
            vector<int> temp;
            for (auto j : vt[i]) temp.push_back(a[j]);
            sort(temp.begin(), temp.end());
            for (int j = 0; j < vt[i].size(); j++) a[vt[i][j]] = temp[j];
        }
        vector<int> ans;
        for (int i = 1; i <= n; i++) ans.push_back(a[i]);
        return ans;
    }
};
```

代码如上，很短。

要点在于：

1. 朋友的朋友是朋友，链状体系里能互换
2. 采用根管理各个体系，sort是上一条的集中体现。
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/57977c699d00479a9cd90fb4ca57b859），发布于 2020-07-16。
