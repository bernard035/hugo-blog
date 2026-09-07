---
title: "排列计算 差分"
date: 2020-05-13T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
如果通过僵硬地涂色来计算单点权重，2e5\*2e5必然TLE。

差分+前缀和可以完美地解决这个问腿。

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=200005;
ll num[N];
int main() {
    ll n, m ;
    cin >> n >> m;
    while (m--) {
        ll l , r ;
        cin >> l >> r;
        num[l]++;
        num[r + 1]--;
    }
    for (int i = 2; i <= n; ++i) num[i] += num[i - 1];
    sort(num + 1, num + 1 + n);
    ll ans = 0;
    for (int i = 1; i <= n; ++i) ans += i * num[i];
    cout << ans << endl;
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/5ddb99099b134da99e13f231cb94b731），发布于 2020-05-13。
