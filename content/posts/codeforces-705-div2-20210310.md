---
title: "CodeForces 705 div2  E"
date: 2021-03-10T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
## 思路

- 首先考虑[l,r]分隔较远的情况。那么一定可以找到最高位为零1，其后全为0的数x(eg.1000)，和x-1(eg.111),(x-1)^x=1111，当且仅当这一情况（存在这一跨度，即最高位不为0）下，可以保证答案为长度为n且全为1的串。
- 接下来考虑lr最高位相同的情况。可以发现，当选择区间长度为偶数时，偶数次的前缀都会被置零，其中最高位必然被置零。也就是说，我永远也不可能选长度为偶数的区间。而我选择长度为1的区间，可以保证答案至少是r。
- 那么是否存在答案大于r的情况呢？我们考虑区间的长度，不难发现，过长的区间，只会造成损失，而不会带来更大的收益，原因同上，偶数次reset。那么考虑截取一段“偶奇偶”的段，这样的一段，异或和为最大的偶数+1。故可选最大的偶数，答案为它+1。也与上述的最大数为r的奇数情况吻合。

## Solution

```
#include <bits/stdc++.h>
using namespace std;
const int N = 1e6 + 5;
int n;
char a[N], b[N];
inline void addOne() {
    bool add = 1;
    for (int i = n - 1; ~i && add; --i) {
        if (add) a[i]++, add = 0;
        if (a[i] == '2') a[i] = '0', add = 1;
    }
}
inline bool same() {
    for (int i = 0; i < n; ++i)
        if (a[i] != b[i]) return 0;
    return 1;
}
inline bool chk() {
    if (b[n - 1] != '0') return 0;
    if (b[n - 2] == '1') {
        for (int i = 0; i < n - 2; ++i)
            if (a[i] != b[i]) return 1;
        return a[n - 2] == '0' && a[n - 1] == '0';
    } else {
        if (same()) return 0;
        addOne();
        if (same()) return 0;
        return 1;
    }
    return 0;
}

int main() {
    scanf("%d%s%s", &n, a, b);
    if (a[0] != b[0])
        for (int i = 0; i < n; ++i) putchar('1');
    else {
        if (chk()) b[n - 1] = '1';
        puts(b);
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/29af672bee124ea19ccadd4735734296），发布于 2021-03-10。
