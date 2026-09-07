---
title: "模拟退火"
date: 2021-08-18T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "模拟"]
---
```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
double k;
double F(double x) {
    return 6 * pow(x, 7) + 8 * pow(x, 6) + 7 * pow(x, 3) + 5 * x * x - k * x;
}
const double eps = 1e-8;
double solve() {
    double T = 50;        // 步长
    double delta = 0.98;  // 降温系数
    double x = 50;        // 初始温度
    double now = F(x);    // 初始答案
    while (T > eps) {
        int f = rand() % 2 ? 1 : -1;
        double xx = x + f * T;
        if (0 <= xx and xx <= 100) {
            double nxt = F(xx);
            if (nxt < now) {
                x = xx;
                now = nxt;
            }
        }
        T *= delta;
    }
    return now;
}
signed main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%lf", &k);
        printf("%.4lf\n", solve());
    }
    return 0;
}
```

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
double k;
double F(double x) {
    return 6 * pow(x, 7) + 8 * pow(x, 6) + 7 * pow(x, 3) + 5 * x * x - k * x;
}
const double eps = 1e-8;
double solve() {
    double T = 50;        // 步长
    double delta = 0.98;  // 降温系数
    double x = 50;        // 初始温度
    double y = F(x);      // 初始答案
    while (T > eps) {
        int f = rand() % 2 ? 1 : -1;
        double xx = x + f * T;
        if (0 <= xx and xx <= 100) {
            double yy = F(xx);
            if (yy < y) {
                x = xx;
                y = yy;
            }
        }
        T *= delta;
    }
    return y;
}
signed main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%lf", &k);
        printf("%.4lf\n", solve());
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/c7094220a4ad4d51a90806b30f18dee7），发布于 2021-08-18。
