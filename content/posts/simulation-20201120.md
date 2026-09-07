---
title: "完全k叉树 模拟"
date: 2020-11-20T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "模拟"]
---
完全k叉树，每层节点数量是已知的，直接推过去模拟就可以了

```
class Solution {
   public:
    long long tree2(int k, vector<int>& a) {
        long long ans = 0;
        int n = a.size();
        int fa = 0;
        for (int i = 1; i < n;) {                      //第i个节点
            for (int j = 0; j < k && i < n; ++j, ++i)  //每遍历k个换一次父亲
                ans += (a[fa] ^ a[i]);
            ++fa;
        }
        return ans;
    }
};
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/05a158d040274a87a1ed12033a40e1b8），发布于 2020-11-20。
