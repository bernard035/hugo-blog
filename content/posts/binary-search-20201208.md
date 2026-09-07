---
title: "字符串 子序列 双指针 二分"
date: 2020-12-08T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "二分", "字符串"]
---
这手双指针堪称妙手。

```
class Solution {
   public:
    int Maximumlength(string x) {
        int n = x.length(), mx = 0;
        int na = 0, nb = 0, nc = 0;
        for (int i = 0; i < n; i++)
            if (x[i] == 'b') nb++;
        int L = 0, R = n - 1;
        while (L <= R) {
            while (x[L] != 'a') {
                if (x[L] == 'b') nb--;
                ++L;
            }
            while (x[R] != 'c') {
                if (x[R] == 'b') nb--;
                --R;
            }
            na++, nc++;
            mx = max(mx, min(na, min(nb, nc)) * 3);
            L++, R--;
        }
        return mx;
    }
};
```

另外二分写法比赛的时候没完成，非常丢人。

```
class Solution {
   public:
    string t = "abc";
    bool chk(int x, string s) {
        int cnt = 0, n = s.length(), p = 0;
        for (int i = 0; i < n; ++i) {
            if (s[i] == t[p]) ++cnt;
            if (cnt == x) {
                ++p, cnt = 0;
                if (p == 3) return 1;
            }
        }
        return 0;
    }
    int Maximumlength(string s) {
        int L = 0, R = s.length() / 3, ans = 0;
        while (L <= R) {
            int mid = L + R >> 1;
            if (chk(mid, s)) {
                ans = mid;
                L = mid + 1;
            } else {
                R = mid - 1;
            }
        }
        return ans * 3;
    }
};
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/3f64758d55a440be90f673a1eeb8e40a），发布于 2020-12-08。
