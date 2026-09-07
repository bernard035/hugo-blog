---
title: "WZB's Harem  状压DP"
date: 2021-01-20T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "DP"]
---
状压DP

每一列对于每一行是唯一确定的，这一点契合了状态压缩的特性。

出题人的话：

> 一道状压 dp。首先不考虑皇后的差异性，把所有皇后当作是一样的，在第 i 行第 k 列安排一位皇后的方案数为 `f[i][j|(1<<k)] += f[i][j]((j>>k)&1==0)`。因为皇后不可能是一样的，所以最后的结果为 `f[n][1<<(n-1)]*(n!)`。

```
#include <bits/stdc++.h>
typedef long long ll;
using namespace std;
const int MOD = 1e9 + 7;
const int maxn = 22;
const int maxm = (1 << 21) + 10;
int n;
int g[maxn][maxn];   //图
int s[maxn][maxm];   // s[x][]表示有x个1的所有状态
int p[maxn];         //行游标
int dp[maxn][maxm];  // n行 m个状态 里有多少个合法情况
int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; ++i)
        for (int j = 1; j <= n; ++j) scanf("%d", &g[i][j]);
    for (int i = 0; i < (1 << n); ++i) {
        //枚举所有长度为n的二进制串，表示一行的可选状态
        int cnt = __builtin_popcount(i);  // i里有多少个1
        s[cnt][++p[cnt]] = i;             // s[x][]表示x个1的所有状态
    }
    dp[0][0] = 1;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (g[i][j]) continue;  //不可选
            for (int k = 1; k <= p[i - 1]; k++) {
                //枚举i-1个1的所有状态：之前选的所有状态
                int pre = s[i - 1][k];  //前面几行可能的选择状态
                if ((pre >> (j - 1)) & 1) continue;
                //不能有同一列 且 不可达此状态 这正是状压的可行性
                int cur = pre | (1 << (j - 1));  //把从右往左第j位置1
                dp[i][cur] = (dp[i][cur] + dp[i - 1][pre]) % MOD;
                // 当前状态
            }
        }
    }
    ll fac = 1;
    for (ll i = 1; i <= n; i++) fac = fac * i % MOD;  //全排列
    printf("%lld\n", fac * dp[n][(1 << n) - 1] % MOD);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/8d78d4985aeb462da234eda65e46b67a），发布于 2021-01-20。
