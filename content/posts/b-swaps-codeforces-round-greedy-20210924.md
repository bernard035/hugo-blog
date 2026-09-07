---
title: "桶 贪心 B. Swaps Codeforces Round #743 (Div. 2)"
date: 2021-09-24T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "Codeforces", "贪心"]
---
![图片说明](/images/084a80941d5ad7f7.png "图片标题")

1. 对于每个a[i] 找第一个大于它的b[j]- 用桶找- 亦可线段树

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 2e5 + 7;
const int mod = 1e9 + 7;
signed main() {
    int T;
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        unordered_map<int, int> p, q;
        for (int i = 0, x; i < n; ++i) cin >> x, p[x] = i;
        for (int i = 0, x; i < n; ++i) cin >> x, q[x] = i;
        // 对于每个a[i] 第一个大于它的b[j]
        vector<int> f(n, n);
        int last = n;
        for (int i = n * 2; i; i -= 2) {
            int &ori = f[p[i - 1]];
            int &pos = q[i];
            ori = min(ori, pos);
            ori = min(ori, last);
            last = ori;
        }
        // 统计答案
        int ans = n * 2;
        for (int i = 0; i < n; ++i) ans = min(ans, i + f[i]);
        cout << ans << '\n';
    }
    return 0;
}
```

如果题目没有限制数据范围，就需要sort+二分或者set二分确定最右小值是谁
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/9e2228d409f04e5c85cb470952c6f6a6），发布于 2021-09-24。
