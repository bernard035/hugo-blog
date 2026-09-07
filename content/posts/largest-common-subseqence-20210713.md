---
title: "最长公共子序列 Largest Common Subseqence"
date: 2021-07-13T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
[pecco](https://www.zhihu.com/column/c_1182444932760125440)

```
#include <bits/stdc++.h>
using namespace std;
const int N = 1005;
int dp[N][N];  // 可采用滚动数组优化 只保留i/i-1行
string s, t;
int main() {
    while (cin >> s >> t) {
        int ns = s.length(), nt = t.length();
        for (int i = 1; i <= ns; ++i) {
            for (int j = 1; j <= nt; ++j) {
                if (s[i - 1] == t[j - 1])
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                else
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        cout << dp[ns][nt] << '\n';
    }
    return 0;
}
```

![图片说明](/images/87efdffcc863c57a.png "图片标题") ![图片说明](/images/c260a7ce3e03924f.png "图片标题")
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/5883e95a19364d66aca027f07949e0f4），发布于 2021-07-13。
