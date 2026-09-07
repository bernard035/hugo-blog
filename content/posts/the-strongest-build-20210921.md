---
title: "The Strongest Build 暴搜"
date: 2021-09-21T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;

const int N = 11;

set<vector<int>> ban;
vector<int> a[N];
ll sum = 0, ans = 0;
vector<int> now, best;
int n;
bool dfs(int i) {
    if (i == n) {
        if (ban.count(now)) return 1;
        if (ans < sum) best = now, ans = sum;
        return 0;
    }

    bool was = 0;
    for (int j = a[i].size() - 1; j >= 0; --j) {
        sum += a[i][j];
        now.emplace_back(j + 1);
        bool ret = dfs(i + 1);
        now.pop_back();
        sum -= a[i][j];
        if (ret)
            was = 1;
        else
            break;
    }
    return was;
}

int main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    cin >> n;
    for (int i = 0, k; i < n; ++i) {
        cin >> k;
        a[i].resize(k);
        for (int& x : a[i]) cin >> x;
    }

    int m;
    cin >> m;
    for (int i = 0; i < m; ++i) {
        vector<int> t(n);
        for (int& x : t) cin >> x;
        ban.insert(t);
    }

    dfs(0);
    for (const int& x : best) cout << x << ' ';
    cout << '\n';
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/d063a749b7554cea85aa05b6b3d24e75），发布于 2021-09-21。
