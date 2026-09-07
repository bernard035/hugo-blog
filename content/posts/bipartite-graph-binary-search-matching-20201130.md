---
title: "二分图匹配"
date: 2020-11-30T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["学习笔记", "二分"]
---
## 二分图

如果能将一个图的点集分为两部分，每一部分内部无边相连，就构成了二分图。

![ ](/images/9b5530576db41e02.png)

所有二分图的题都能用网络流来写。

## 增广路

![](/images/9e41f33dd478421b.png)

## 匈牙利算法

匈牙利算法是[我们很早就接触过的](http://acm.hdu.edu.cn/showproblem.php?pid=2063)

给定男生人数和女生人数，以及他们的互相follow的关系，求最大配对数量。

```
#include <bits/stdc++.h>
using namespace std;
const int N = 510;
//匈牙利算法O(n^3)
int n, m;     //男生 女生
int g[N][N];  // graph 描述图关系
int vis[N], boy[N];

bool dfs(int x) {
    for (int i = 1; i <= n; i++) {  //遍历每个男生
        if (!vis[i] && g[x][i]) {   //如果存在关系且没有被访问过
            vis[i] = 1;             //访问记录
            if (!boy[i] || dfs(boy[i])) {  //如果男生名花无主或者可以挪动
                boy[i] = x;
                return 1;
            }
        }
    }
    return 0;
}

int main() {
    int k;
    while (cin >> k && k) {
        cin >> m >> n;
        memset(g, 0, sizeof(g));
        memset(boy, 0, sizeof(boy));
        for (int i = 0; i < k; i++) {
            int a, b;
            cin >> a >> b;
            g[a][b] = 1;
        }
        int sum = 0;
        for (int i = 1; i <= m; i++) {    //遍历每一个女生
            memset(vis, 0, sizeof(vis));  //每一个女生都清空访问记录
            if (dfs(i)) sum++;
        }
        cout << sum << endl;
    }
    return 0;
}
```

## konig定理

![图片说明](/images/b17a21fe884de476.png)  
![图片说明](/images/b4e4aaad70f42d1b.png)  
![图片说明](/images/a39bce48eb3eee41.png)

## Kuhn-Munkres算法

最优匹配是建立在**完美匹配**的基础上的，如果不存在完美匹配，那么本算法失效（但是，我们可以人为连一些权值为0的边，甚至加点，使得没有匹配的节点们最后都有一个“虚假”的匹配）。

算法思路：  
最开始将每个左边节点连的权值最大的边视为有效边，在匹配给过程当中如果某个点找不到匹配点，则选择将一些无效边改成有效边继续去匹配，选择的这些无效边其实是换掉的原来的某条有效边，那么我们肯定选换掉的代价最小的。

具体操作：

- 给每个点预设一个顶标，只有两个端点的顶标加起来等于边权的边，我们才认为是有效边，即L[x]+R[y]==w[x][y]的时候才是有效边- 最开始左集合的每个点的顶标为他连出去权值最大的边的权值，右集合每个点顶标为0，当匹配失败的时候，我们遍历之前的左集合本次尝试匹配遍历过的点，在他们的连向右集合未遍历的点的边中找一个与顶标和差值最小的边，即delta=w[x][y]-L[x]-R[y]最小的边（可理解为找出一条损失最小的找出一条增广路。）找到之后修改顶标：左侧所有遍历过的点减去delta，右边所有遍历过的点加上delta，这样原来的有效边还是有效边，而我们新加的边也已经加上了。- 重复找匹配+修改顶标的操作，一直到找到一个完美匹配即可。
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/e5ff9babe0e04fa49e49dbce82e6fa2e），发布于 2020-11-30。
