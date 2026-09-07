---
title: "2021 牛客寒假多校3 线段树 思维 暴力 博弈"
date: 2021-02-19T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "牛客", "线段树", "博弈"]
---
## 重力坠击

暴搜即可

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 17;
const int mod = 1e9 + 7;
struct poi {
    int x, y, r;
} a[N];
bool alive[N];
int n, k, R;
int dfs(int lv) {
    if (lv == k) {
        int ans = 0;
        rep(i, 1, n) ans += alive[i];
        return n - ans;
    }
    int ans = 0;
    vector<int> nowsta;
    rep(x, -7, 7) {
        rep(y, -7, 7) {
            rep(i, 1, n) if (sqrt((a[i].x - x) * (a[i].x - x) +
                                  (a[i].y - y) * (a[i].y - y)) <= a[i].r + R &&
                             alive[i]) nowsta.push_back(i),
                alive[i] = 0;
            int nxt = dfs(lv + 1);
            ans = max(nxt, ans);
            for (const int &v : nowsta) alive[v] = 1;
            nowsta.clear();
        }
    }
    return ans;
}
int main() {
    sc(n), sc(k), sc(R);
    rep(i, 1, n) sc(a[i].x), sc(a[i].y), sc(a[i].r);
    rep(i, 0, 15) alive[i] = 1;
    pr(dfs(0));
    return 0;
}
```

## 匹配串

本题其实就是一道思维题。

1. 因为给定的串至少有一个#，所以要么 0 要么 无穷
2. 既然有了 # 那中间是什么都不重要了
3. 只需要对左右两边进行匹配即可，看涵盖范围是否需要扩大，如果存在不一样的前缀或后缀，直接就不可能了

```
n = int(input())
ans = -1
s = t = ''
for _ in range(n):
    a = input().split('#')
    L = a[0]
    R = a[-1]
    if s in L: s = L
    elif L not in s: ans = 0
    if t in R: t = R
    elif R not in t: ans = 0
print(ans)
```

## 糖果

并查集即可，最终还是要满足朋友的朋友是朋友。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 1e6 + 7;
const int mod = 1e9 + 7;
int n, m, u, v, a[N];
int ht[N];
int fa[N];
void init(int n) {
    for (int i = 0; i <= n; ++i) fa[i] = i, ht[i] = 0;
}
inline int Find(int x) {
    if (x != fa[x]) fa[x] = Find(fa[x]);  //路径压缩
    return fa[x];
}
void merge(int x, int y) {
    x = Find(x);
    y = Find(y);
    if (ht[x] == ht[y])
        ht[x] = ht[x] + 1, fa[y] = x;
    else if (ht[x] < ht[y])
        fa[x] = y;
    else
        fa[y] = x;
}
unordered_map<int, int> mp;
int main() {
    sc(n), sc(m);
    init(n);
    rep(i, 1, n) sc(a[i]);
    rep(i, 1, m) {
        sc(u);
        sc(v);
        merge(u, v);
    }
    ll ans = 0;
    rep(i, 1, n) {
        int f = Find(i);
        if (mp[f] == 0) {
            mp[f] = a[i];
        } else
            mp[f] = max(mp[f], a[i]);
    }
    rep(i, 1, n) { ans += mp[Find(i)]; }
    pr(ans);
    return 0;
}
```

## 数字串

编程模拟，我的做法是打了个map出来相互转化

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
map<char, string> add;
map<string, char> sub;
int main() {
    for (char i = 'a' + 10; i <= 'a' + 25; ++i) {
        if (i == 't') continue;
        int n = i - 'a' + 1;
        char x = 'a' - 1 + n / 10;
        char y = 'a' - 1 + n % 10;
        string t = "";
        t = t + x + y;
        add[i] = t;
        sub[t] = i;
        //cout << i << " : " << t << endl;
    }
    string s;
    cin >> s;
    int n = s.size();
    for (int i = 0; i < n; ++i) {  //扩
        if (s[i] >= 'k' && s[i] != 't') {
            cout << s.substr(0, i);
            cout << add[s[i]];
            if (i != n - 1) cout << s.substr(i + 1);
            return 0;
        }
    }
    for (int i = 1; i < n; ++i) {  //缩
        if (s[i - 1] == 'a' && s[i] >= 'a' && s[i] <= 'i' ||
            s[i - 1] == 'b' && s[i] >= 'a' && s[i] <= 'f') {
            cout << s.substr(0, i - 1);
            cout << sub[s.substr(i - 1, 2)] << s.substr(i + 1);
            return 0;
        }
    }
    puts("-1");
    return 0;
}
```

## 序列的美观度

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 1e6 + 7;
const int mod = 1e9 + 7;

unordered_map<int, int> idx;
ll n, m;
ll a[N], dp[N];

int main() {
    sc(n);
    rep(i, 1, n) sc(a[i]);
    rep(i, 1, n) {
        dp[i] = dp[i - 1];
        if (idx.count(a[i])) dp[i] = max(dp[idx[a[i]]] + 1, dp[i]);
        // 如果之前出现过，就可以考虑和之前的凑对
        idx[a[i]] = i;
    }
    pr(dp[n]);
    return 0;
}
```

## 加法和乘法

首先特判$n=1$的情况，胜利条件先手奇数后手偶数，下面讨论$n\ne 1$的情况：

1. 奇数加奇数得偶数，奇数加偶数得奇数，偶数加偶数得偶数
2. 奇数乘奇数得奇数，奇数乘偶数得偶数，偶数加偶数得偶数
3. 当$n=2$时，无论是奇偶、奇奇、偶偶，都可以构成偶数，所以如果后手在$n=2$时操作，后手必胜。

接下来考虑其他情况：

1. 先手在$n=2$时面对的局面则不一样，只有存在奇数，先手才能赢，也就是说，先手必须将偶数数量控制在1个及以下。
2. 基于上述分析，后手的策略应当是消除奇数、增加偶数。
3. 而偶数在一个回合内是可以维持稳定的。也就是说，如果任意时刻，先手的局面是存在偶数的，他会消除一个偶数，而后手会增加一个偶数。最终偶数数量维持平衡。
4. 而现在是先手完成最后一个可执行回合，先手如果能把唯一一个偶数消除，就能赢。

所以如果存在超过1个偶数，先手必败。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
int main() {
    ll n, x;
    sc(n);
    if (n == 1) {
        sc(x);
        puts(x & 1 ? "NiuNiu" : "NiuMei");
    } else if (n & 1) {
        puts("NiuMei");
    } else {
        int cnt = 0;
        rep(i, 1, n) sc(x), cnt += (x % 2 == 0);
        puts(cnt < 2 ? "NiuNiu" : "NiuMei");
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/a9a6b452c75744dd814fa2f1164840b7），发布于 2021-02-19。
