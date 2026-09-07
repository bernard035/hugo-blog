---
title: "树形DP maki和tree"
date: 2020-09-12T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "DP"]
---
```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 1e5 + 10;
int n;
ll res, dp[N][2];
char str[N];
vector<int> g[N];
void dfs(int x, int fa) {
    if (str[x] == 'W') dp[x][0] = 1;  //表示子树中无黑色路径数量
    for (int to : g[x]) {
        //遍历根为x的子树
        if (to == fa) continue;  //不回头
        dfs(to, x);
        if (str[x] == 'B') res += dp[x][0] * dp[to][0] + dp[to][0];
        //之前走过的子树中的白色路径数量 乘以 新分支中的白色路径数量
        else
            res += dp[x][1] * dp[to][0] + dp[x][0] * dp[to][1];
        //之前走过的子树中的黑色合法路径数量 乘以 新分支中白色路径数量
        //加上镜像情况
        dp[x][0] += dp[to][0];  //将新分支中情况统计计入本节点
        dp[x][1] += dp[to][1];
    }
    if (str[x] == 'B') dp[x][1] = dp[x][0] + 1, dp[x][0] = 0;
    //如果是黑色点，更新黑色点可行路径量
    //而且黑色点不存在可行的白色点路径
}
int main() {
    cin >> n;
    scanf("%s", str + 1);
    for (int i = 1, x, y; i < n; i++)
        scanf("%d %d", &x, &y), g[x].push_back(y), g[y].push_back(x);
    dfs(1, 1);
    cout << res;
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/3099e7dc6d3e459294fc16b1549f7916），发布于 2020-09-12。
