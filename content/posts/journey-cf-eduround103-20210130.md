---
title: "旅行Journey CF eduRound103 D"
date: 2021-01-30T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "Codeforces"]
---
## 题意

从0号点到n号点排列在直线上。  
n条有向边连接着这些点。  
L表示向左，R表示向右。  
对于每一个点，求它起点最多可达多少个点。

## 思路

- 首先考虑LRLR相间的情况：  
  在这样的子段上，奇数点（从0开始算）可达子段所有点，偶数点所有点都不可达
- 然后考虑RRRR/LLLL的情况：  
  在这样的子段上，每个点都只能到达自己和旁边的一个点，共2个点  
  所有的段都是由这两种情况组成的
- 考虑接壤LLR的情况：

  ```
  0L1R2R3
  1 3 2 1
  ```

  接壤情况不存在相互影响

综合来看，只需要考虑每个点左边是不是L 右边是不是R即可  
如果左边是L，或右边是R，就看顺着方向，看交叉的路能走到多远

## Solution

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
int main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    int T, n;
    cin >> T;
    string s;
    while (T--) {
        cin >> n >> s;
        vector<int> L(n), R(n); // 左右延伸距离
        L[0] = 1;
        for (int i = 1; i < n; i++) {
            if (s[i] == s[i - 1])
                L[i] = 1;
            else
                L[i] = L[i - 1] + 1;
        }
        R[n - 1] = 1;
        for (int i = n - 2; i >= 0; i--) {
            if (s[i] == s[i + 1])
                R[i] = 1;
            else
                R[i] = R[i + 1] + 1;
        }
        for (int i = 0; i <= n; i++) {
            int ans = 1;
            if (i) ans += s[i - 1] == 'L' ? L[i - 1] : 0;
            if (i != n) ans += s[i] == 'R' ? R[i] : 0;
            cout << ans << ' ';
        }
        cout << '\n';
    }
    return 0;
}

/*
0 1 2 3
 l r l
 r l r
*/
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/cb4ab8275a6542dc9a029142934fefb7），发布于 2021-01-30。
