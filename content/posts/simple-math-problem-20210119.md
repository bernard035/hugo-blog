---
title: "A Simple Math Problem 容斥原理"
date: 2021-01-19T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
## 题意

给定一个数$n$，求$\sum _{i=1}^n \sum _{j=1}^i [gcd(i,j)=1]F(j)$，其中$F(i)$表示的是$i$的数位和。

## 思路

$$\sum _{i=1}^n \sum _{j=1}^i [gcd(i,j)=1]F(j) = \sum _{i=1}^n \sum _{j=i+1}^n [gcd(i,j)]F(i) +1$$

题目要我们求对于每个数$i\in [1,n]$，所有$j\in [1,i]$与$i$互质的$F(j)$的和。

可以将其转化成反向的：对于每个数$i\in [1,n]$，所有$j\in [i+1,n]$与$i$互质的数的个数，这就是$F(i)$的权重。

这一对称情况忽视了对角线上的$gcd(1,1)$，所以要将其补上。

本题亦可使用莫比乌斯反演推导。

## solution

```
#include <bits/stdc++.h>
using namespace std;
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld", (x))
const int N = 1e5 + 7;
typedef long long ll;
bool vis[N];
int cnt;
ll f[N];  //数位和

vector<int> prime;  //质数表
vector<int> p[N];   // p[i]里存着i的所有质因子

inline int getf(int x) {
    int res = 0;
    while (x) {
        res += x % 10;
        x /= 10;
    }
    return res;
}

inline void pre(ll n) {
    for (int i = 2; i < N; ++i) {  //筛出所有质数
        if (!vis[i]) ++cnt, prime.push_back(i);
        for (int j = 0; j < cnt && i * prime[j] < N; ++j) {
            vis[i * prime[j]] = 1;
            if (i % prime[j] == 0) break;
        }
    }
    for (int i = 2; i <= n; ++i) {  //筛出每个数所有的质因数
        int x = i;
        for (int j = 0; j < cnt && prime[j] * prime[j] <= x; ++j) {
            if (x % prime[j] == 0) {
                p[i].push_back(prime[j]);
                while (x % prime[j] == 0) x /= prime[j];
            }
        }
        if (x != 1) p[i].push_back(x);
    }
    for (int i = 1; i <= n; ++i) f[i] = getf(i);  //得到每个数的数位和
}
int main() {
    ll n;
    sc(n);
    pre(n);
    //对于每一个i，找[i+1,n]里有多少个个数和它互质
    // fi*数量 即是权重
    ll ans = 0;
    for (int i = 1; i < n; ++i) {
        int m = p[i].size();
        ll tmp = 0;  // 与i不互质的数的个数
        for (int j = 1; j < 1 << m; ++j) {
            int c = __builtin_popcount(j);  //选中了多少个因数
            ll op = (c & 1) ? 1 : -1;       //容斥奇加偶减
            ll ret = 1;  //当前枚举状态的所有质因数的乘积
            for (int k = 0; k < m; ++k)
                if (j & (1 << k)) ret *= p[i][k];
            ll now = (n - i) / ret;  //有多少个数是ret的倍数
            tmp += op * now;
        }
        ans += (n - i - tmp) * f[i];
    }
    pr(ans + 1); //gcd(1,1)未被计算
    return 0;
}
```

## 出题人题解

![图片说明](/images/b5bb427497c7d189.png "图片标题")
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/c0fc40e153c74c809a30ebb947b1c147），发布于 2021-01-19。
