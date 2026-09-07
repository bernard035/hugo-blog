---
title: "牛牛摆玩偶 二分"
date: 2020-11-27T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "二分"]
---
二分间距然后模拟即可

```
class Solution {
   public:
    typedef long long ll;
    static bool cmp(const Interval& a, const Interval& b) {
        return a.start < b.start;
    }
    bool chk(int n, int m, const vector<Interval>& a, int d) {
        int x = a[0].start, endx = a[m - 1].end;
        --n; //第一个点落在第一个区间的起始点上
        int p = 0;
        for (int i = x; n && i <= endx; --n) {
            if (a[p].end - i >= d)
                i += d;                                 //不需要跨区间
            else {                                      //需要跨区间
                while (p < m && a[p].end - i < d) ++p;  //找到可落子的区间
                int nowd = max(d, a[p].start - i);
                //考虑下个区间的第一个点距离当前点较远的情况
                i += nowd;
                if (m == p && n)
                    return 0;  //如果区间枚举完了，但点还没用完，该间距不行
            }
        }
        return n == 0;  //点恰好用完
    }
    int doll(int n, int m, vector<Interval>& a) {
        sort(a.begin(), a.end(), cmp);
        int d = a[m - 1].end - a[0].start;
        ll L = 1, R = d, ans = L;
        while (L <= R) {  //二分枚举答案
            ll mid = (L + R) / 2;
            if (chk(n, m, a, mid))
                ans = mid, L = mid + 1;
            else
                R = mid - 1;
        }
        return ans;
    }
} pp;
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/bbe64530abf942668a8251eafd60aaaf），发布于 2020-11-27。
