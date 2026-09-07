---
title: "坑贪心 二进制枚举 CCPC长春A"
date: 2021-05-19T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "CCPC", "贪心", "模拟"]
---
![图片说明](/images/acfc592a164ec279.png "图片标题")

```
#include <bits stdc++.h>
using namespace std;
int v[10] = {1, 6, 28, 88, 198, 328, 648},
    e[10] = {8, 18, 28, 58, 128, 198, 388};
int main() {
    int T, n;
    scanf("%d", &amp;T);
    while (T--) {
        scanf("%d", &amp;n);
        int res = 0;
        for (int i = 0; i &lt; (1 &lt;&lt; 7); ++i) {
            int t = 0, s = 0;
            for (int j = 0; j &lt; 7; ++j)
                if (i &gt;&gt; j &amp; 1) s += v[j], t += e[j];
            if (s &lt;= n) res = max(res, t + n * 10);
        }
        printf("%d\n", res);
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/e5d22b343c374153a72af2a417c2d8d0），发布于 2021-05-19。
