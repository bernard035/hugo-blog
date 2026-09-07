---
title: "栈和排序"
date: 2020-06-02T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
## 题意

输出字典序最大的出栈序列

## 思路

输入的数是1-n，要找字典序最大，肯定是**让n尽可能在最前面**，然后是n-1……然后一直到1.

设置一个目标值，初始为n，找到目标值就直接打印，然后目标值-1，然后在栈里面找目标值，注意要用while来完成这一步。

非常感谢钟涛大佬发现原本的代码问题+数据水，刘晟大佬帮忙指出while来更新栈。

## solution

```
#include <stdio.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d ", (x))
const int N = 1e6 + 7;
int a[N], n, st[N], pos;
int main() {
    sc(n);
    for (int i = 0; i < n; ++i) sc(a[i]);
    int tar = n;
    for (int i = 0; i < n; ++i) {
        while (pos && tar == st[pos - 1]) pr(st[--pos]), tar--;
        if (a[i] == tar) pr(a[i]), tar--;
        else st[pos++] = a[i];
    }
    while(pos) pr(st[--pos]);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/ef9e6fb71d974045b6d6bd063b63d618），发布于 2020-06-02。
