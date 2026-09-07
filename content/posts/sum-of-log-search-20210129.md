---
title: "Sum of Log 记忆化搜索"
date: 2021-01-29T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "搜索"]
---
## 题意

给定$X$和$Y$，要求计算

$$\Large{\sum_{i=0}^X\sum_{j=[i=0]}^Y[i\&j=0]\lfloor log_2(i+j)+1 \rfloor}$$

## 思路

由于在贡献计算公式中$X,Y$可交换，另外$\large{\sum_{i=0}^X\sum_{j=[i=0]}^Y}$其实就是$\large{\sum_{i=0}^X\sum_{j=0}^Y}$跳过$i,j$同时为零的情况，所以$X,Y$可交换。

所以对于题目要算什么的理解，其实就是枚举所有$i\&j\ne 0$的$<i,j>$，其对答案的贡献就是$i+j$的二进制串长。

记忆化搜索的本质就是搜索树的复用。

## Solution

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int mod = 1e9 + 7;
bool a[35], b[35];
ll dp[35][2][2];
ll ans = 0;
ll dfs(int pos, bool limit1, bool limit2, bool zero) {
    if (pos == -1) return 1;  // 合法返回
    if (dp[pos][limit1][limit2] != -1) return dp[pos][limit1][limit2];
    bool up1 = limit1 ? a[pos] : 1;
    bool up2 = limit2 ? b[pos] : 1;
    ll tmp1 = 0, tmp2 = 0;
    for (int i = 0; i <= up1; ++i) {
        for (int j = 0; j <= up2; ++j) {
            if (i & j) continue;  // 题目要求
            ll d = dfs(pos - 1, limit1 && i == up1, limit2 && j == up2,
                       zero || (i ^ j));
            // 注意这里的 zero || (i ^ j)
            // 意思是如果当前位之前枚举的都为0，这一位如果是1就将它做最高位，否则就让之前的做最高位，因为只有最高位对答案有贡献
            if (!zero && (i ^ j)) tmp1 = (tmp1 + d) % mod;
            // 如果这是最高位，则把贡献累加一下
            tmp2 = (tmp2 + d) % mod;  // 记录一下记忆化的答案
        }
    }
    ans = (ans + tmp1 * (pos + 1)) % mod;
    // 贡献 * 最高位1到最低位的长度
    dp[pos][limit1][limit2] = tmp2;  // 记忆化
    return tmp2;
}
inline void solve(int x, int y) {
    int pos1 = 0, pos2 = 0;  // pos1:最大数的二进制位数
    do a[pos1++] = x & 1 ;while(x >>= 1);
    while (y) b[pos2++] = y & 1, y >>= 1;
    dfs(pos1 - 1, 1, 1, 0);
}
int main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    int t, x, y;
    cin >> t;
    while (t--) {
        memset(dp, -1, sizeof dp);
        memset(a, 0, sizeof a);
        memset(b, 0, sizeof b);
        ans = 0;
        cin >> x >> y;
        if (x < y) swap(x, y);
        solve(x, y);
        cout << ans << endl;
    }
    return 0;
}
```

大佬的代码看起来就是我的拆解展开

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
ll dp[30][2][2];
int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        int x, y;
        scanf("%d%d", &x, &y);
        ll ans = 0;
        memset(dp,0,sizeof dp);
        for (int i=29; i>=0; --i) {
            if ((1<<i)<=x) dp[i][(x>>i)==1][y<(1<<i)]+=i+1;
            if ((1<<i)<=y) dp[i][x<(1<<i)][(y>>i)==1]+=i+1;
        }
        for (int t=29; t>=0; --t) for (int u=0; u<2; ++u) for (int v=0; v<2; ++v) {
            ll &w = dp[t][u][v];
            if (!w) continue;
            if (t==0) { 
                ans = (ans+w)%1000000007;
                continue;
            }
            if (!u||(x>>t-1)&1) dp[t-1][u&&(x>>t-1&1)][v&&(y>>t-1&1^1)] += w;
            if (!v||(y>>t-1)&1) dp[t-1][u&&(x>>t-1&1^1)][v&&(y>>t-1&1)] += w;
            dp[t-1][u&&(x>>t-1&1^1)][v&&(y>>t-1&1^1)] += w;
        }
        printf("%lld\n", ans);
    }
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/8febf1cb0dcb4f25a3907add84634bde），发布于 2021-01-29。
