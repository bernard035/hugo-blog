---
title: "二分法解方程"
date: 2020-05-17T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "二分"]
---
没什么可说的，记录一下写法，以及long double这种精度

引用自[兰子大佬](https://ac.nowcoder.com/discuss/428377?type=101&order=0&pos=5&page=1)

> 很容易发现左边是一个单调增的函数，所以二分求解即可。值得注意的是如果用double可能出现tle的情况（实测double精度有问题，导致后面$r−l$无限不动）。解决方法有两种，一种是换long double，另外一种是进行足够多次二分即可（例如1000次二分），可以避免tle的出现。

青竹大佬的

```
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e5 + 7, mod = 1e9 + 7;
const double eps = 1e-8;
typedef long long ll;
double a, b, c;
bool check(double x) {
    return pow(x, a) + b * (log(x)) >= c;
}
int main()
{
    scanf("%lf%lf%lf", &a, &b, &c);
    double left = 0, right = 1e9, ans = -1;
    while (right - left > eps) {
        double mid = (left + right) / 2;
        if(mid - left < eps) {
            ans = left;
            break;
        }
        if(check(mid)){
            right = mid;
        }
        else
            left = mid;
    }
    printf("%.8f\n", ans);
}
```

兰子大佬的

```
#include <bits/stdc++.h>
using namespace std;
#define ld long double
int a;
ld b, c;
ld bs(ld l, ld r) {
    if (r - l <= 1e-8) return l;
    ld mid = (r + l) / 2;
    ld res = 1;
    for (int i = 0; i < a; i++) res *= mid;
    res += b * log(mid);
    if (res < c) return bs(mid, r);
    return bs(l, mid);
}
int main() {
    cin >> a >> b >> c;
    printf("%.7Lf", bs(1, c));
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/8f2b27b163764e84b597f5ca3f2bcf07），发布于 2020-05-17。
