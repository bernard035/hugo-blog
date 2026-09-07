---
title: "Walker 数学 浮点二分"
date: 2021-01-28T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "二分", "数学"]
---
## 题意

线段$[0,n]$上有两个人，位置和速度分别是$p_1,p_2,v_1,v_2$。

求他们最短把线段覆盖（走）完的时间。

## Solution

```
#include <bits/stdc++.h>
using namespace std;
inline double ct(double x, double pos, double v) {
    //当前处于pos，要覆盖[0,x]，速度v的最短时间
    return (min(pos, x - pos) + x) / v;
}
int main() {
    int t;
    scanf("%d", &t);
    while (t--) {
        double n, p1, v1, p2, v2;
        scanf("%lf%lf%lf%lf%lf", &n, &p1, &v1, &p2, &v2);
        if (p1 > p2) {  // 按左右排序
            swap(p1, p2);
            swap(v1, v2);
        }
        // 情况1 : 一个人走完所有
        double ans = min(ct(n, p1, v1), ct(n, p2, v2));
        // 情况2 : 对穿
        ans = min(ans, max((n - p1) / v1, p2 / v2));
        // 情况3 : 在两者之间找一个点，一人负责一边
        double left = p1, right = p2;
        for (int i = 0; i < 100; i++) {
            double mid = (left + right) / 2;
            double time_left = ct(mid, p1, v1);
            double time_right = ct(n - mid, p2 - mid, v2);
            ans = min(ans, max(time_left, time_right));
            if (time_left > time_right) {
                right = mid;
            } else {
                left = mid;
            }
        }
        printf("%.10f\n", ans);
    }
    return 0;
}
```

python被卡的死死的

```
def tm(x, p, v):
    return min((x + p), (x - p) + x) / v

T = int(input())
for _ in range(T):
    n, p1, v1, p2, v2 = map(float, input().split())
    if p1 > p2:
        p2, p1 = p1, p2
        v2, v1 = v1, v2
    # situation 1
    ans = min(tm(n, p1, v1), tm(n, p2, v2))
    # situation 2
    ans = min(ans, max((n - p1) / v1, p2 / v2))
    # situation 3
    L = p1
    R = p2
    for _ in range(100):
        mid = (L + R) / 2
        tl = tm(mid, p1, v1)
        tr = tm(n - mid, n - p2, v2)
        ans = min(ans, max(tl, tr))
        if tl > tr: R = mid
        else: L = mid
    print(ans)
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/106843e2ab754c31beb64d4b41504f8c），发布于 2021-01-28。
