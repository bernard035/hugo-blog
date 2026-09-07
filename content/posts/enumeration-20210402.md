---
title: "数字串 枚举 优化 暴力 思维"
date: 2021-04-02T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "模拟"]
---
```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 5e5 + 7;
inline bool cmp(char a[], char b[]) {
    for (int i = 1; a[i] && b[i]; ++i)
        if (a[i] != b[i]) return a[i] > b[i];
    return 1;  // equal
}
char L[50], R[50], s[N];
int main() {
    scanf("%s%s%s", L + 1, R + 1, s + 1);
    int lenL = strlen(L + 1), lenR = strlen(R + 1), lenS = strlen(s + 1);
    ll ans = 0, preZero = 0;
    int left = 0, right = 0, now;
    for (int i = 1, sz = lenS - lenL + 1; i <= sz; ++i) {
        if (s[i] == '0')
            ++preZero;
        else {  // 枚举以s[i]开头的合法区间，其实只会在目标位置附近浮动一位，那么检测一下即可
            left = i + lenL - 1, right = i + lenR - 1;
            if (!cmp(s + i - 1, L)) ++left;
            if (right > lenS)
                right = lenS;
            else if (!cmp(R, s + i - 1))
                --right;
            now = right - left + 1;
            ans += now;
            ans += preZero * now;
            preZero = 0;
        }
    }
    printf("%lld", ans);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/67395b935d5b46a69902318370421d27），发布于 2021-04-02。
