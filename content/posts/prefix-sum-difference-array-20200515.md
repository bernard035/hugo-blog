---
title: "前缀和 差分"
date: 2020-05-15T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
和校门口的树是一样的。只不过校门口的树数据太水了。

对于$1<=n,m<=1e8$，前缀和 + 差分可以满足需求，再大就需要离散化，这个离散化还是稍有难度的（暂时先不写了

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 1e8 + 7;
const ll mod = 1e9 + 7;
inline ll read() {
    ll s = 0, f = 1;
    char ch;
    do {
        ch = getchar();
        if (ch == '-') f = -1;
    } while (ch < 48 || ch > 57);
    while (ch >= 48 && ch <= 57)
        s = (s << 1) + (s << 3) + (ch ^ 48), ch = getchar();
    return s * f;
}
ll vis[N];
int main() {
    ll n = read(), m = read(), a, b, ans=0;
    while (m--) {
        a = read(), b = read();
        --vis[a], ++vis[b + 1];
    }
    for (int i = 1; i <= n; ++i) vis[i] += vis[i - 1];
    for (int i = 0; i <= n; ++i) ans += !vis[i];
    printf("%lld\n", ans);
    return 0;
}
```

这道题的收获其实是：1e8的规模可以a，但是巨量的数据吞吐导致cin即使关闭了与C的关联性，依然会TLE

![图片说明](/images/3864e85cbeb0e080.png "图片标题")

这题也可以使用贪心的思维，对线段进行排序，可以到一样的结果

![ ](/images/9a53a605c89ab803.png "图片标题")

![图片说明](/images/449d024b8d8c5577.png "图片标题")

对离散化的概括
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/8887f7ff9d02444c809ff0833106f4e7），发布于 2020-05-15。
