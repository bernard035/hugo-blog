---
title: "Gitignore 贪心 思维 STL"
date: 2021-01-27T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "贪心"]
---
## 题意

给n个需要忽视的目录，m个需要保护的目录，求gitignore的最小行数

## Solution

用保护去检索ignore。

```
#include <bits/stdc++.h>
using namespace std;
int t, n, m;
vector<string> v1, v2;
set<string> prt, vis;
int main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    cin >> t;
    while (t--) {
        cin >> n >> m;
        string s;
        v1.clear(), v2.clear(), vis.clear(), prt.clear();
        for (int i = 0; i < n; i++) cin >> s, v1.push_back(s);
        for (int i = 0; i < m; i++) cin >> s, v2.push_back(s);
        for (int i = 0; i < m; i++) {
            string s = "";
            for (char &c : v2[i]) {           //建立保护清单
                if (c == '/') prt.insert(s);  //所有的父级目录都被保护
                s += c;
            }
        }
        int ans = n;  //假设全都单独写入gitignore
        for (int i = 0; i < n; i++) {
            string s = "";
            for (char &c : v1[i]) {
                if (c == '/') {
                    if (!prt.count(s)) {  //没有被保护
                        if (vis.count(s)) --ans;  //存在同级目录
                        else vis.insert(s);
                        break;  //子目录不再检索
                    }  //此处是一个贪心的最优：子目录可以，且父目录未被保护，肯定是父目录最优
                }
                s += c;
            }
        }
        cout << ans << endl;
    }
    return 0;
}
```

map写法亦可

```
#include <bits/stdc++.h>
using namespace std;
int t, n, m;
vector<string> v1, v2;
map<string, bool> prt, vis;
int main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    cin >> t;
    while (t--) {
        cin >> n >> m;
        string s;
        v1.clear(), v2.clear(), vis.clear(), prt.clear();
        for (int i = 0; i < n; i++) cin >> s, v1.push_back(s);
        for (int i = 0; i < m; i++) cin >> s, v2.push_back(s);
        for (int i = 0; i < m; i++) {
            string s = "";
            for (char &c : v2[i]) {
                if (c == '/') prt[s] = 1;
                s += c;
            }
        }
        int ans = n;
        for (int i = 0; i < n; i++) {
            string s = "";
            for (char &c : v1[i]) {
                if (c == '/') {
                    if (!prt[s]) {
                        if (vis[s]) --ans;
                        else vis[s] = 1;
                        break;
                    }
                }
                s += c;
            }
        }
        cout << ans << endl;
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/20c6b53558ed4d3bba3135d167ce2d34），发布于 2021-01-27。
