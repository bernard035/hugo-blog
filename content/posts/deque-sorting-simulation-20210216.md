---
title: "牛牛与交换排序 deque 模拟"
date: 2021-02-16T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "模拟"]
---
简单分析后可知

- 第一次操作必然使最小的、不在其位的数让它归位
- 那么长度就已经固定了
- 那么接下来要做的就是模拟，看能否完成排序即可

可以用deque双端队列模拟，也可以用[平衡树](https://ac.nowcoder.com/acm/contest/view-submission?submissionId=46674066)

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
int n, a[N];
deque<int> q;

int main() {
    sc(n);
    rep(i, 1, n) sc(a[i]);
    int x = 0;  //第一个不在正确位置上的数
    // aka 不在正确位置上的 最小的数
    rep(i, 1, n) if (a[i] != i) {
        x = i;
        break;
    }
    if (!x) return puts("yes\n1"), 0;  //当前已经有序
    int now, len;
    rep(i, x + 1, n) if (a[i] == x) {
        now = i;          // 这个最小的错位数 现在在now这个位置
        len = i - x + 1;  // 需要翻转的线段长度
        break;
    }
    rep(i, x, now - 1) q.emplace_front(a[i]);
    bool tag = 0;
    for (int i = x; i + len - 1 <= n; i++) {
        if (!tag)
            q.emplace_front(a[i + len - 1]);
        else
            q.emplace_back(a[i + len - 1]);
        if (tag) {
            if (q.front() != i) tag = 0;
        } else {
            if (q.back() != i) tag = 1;
        }
        if (tag) {
            if (q.front() != i) return puts("no"), 0;
            q.pop_front();
        } else {
            if (q.back() != i) return puts("no"), 0;
            q.pop_back();
        }
    }
    for (int i = n - len + 2; i <= n; i++) {
        if (tag) {
            if (q.front() != i) return puts("no"), 0;
            q.pop_front();
        } else {
            if (q.back() != i) return puts("no"), 0;
            q.pop_back();
        }
    }
    printf("yes\n%d", len);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/436bd1c6ae6042efb162a78b8de74961），发布于 2021-02-16。
