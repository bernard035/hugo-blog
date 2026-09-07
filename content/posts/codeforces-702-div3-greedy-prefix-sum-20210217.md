---
title: "Codeforces #702 div3 贪心 暴力 前缀和"
date: 2021-02-17T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/赛后分析"]
tags: ["赛后分析", "Codeforces", "贪心"]
---
<https://codeforces.com/contest/1490>

## Dense Array

给定一个数组，问至少插入多少个元素，可以使得相邻元素之间，大的值不超过小的值的两倍。

简单贪心模拟。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 105 + 7;
const int mod = 1e9 + 7;
ll a[N];
ll cnt(ll x, ll y) {
    ll ans = 0;
    while (x * 2 < y) {
        x *= 2;
        ++ans;
    }
    return ans;
}
int main() {
    ll T, n;
    sc(T);
    while (T--) {
        sc(n);
        rep(i, 1, n) sc(a[i]);
        ll ans = 0;
        rep(i, 2, n) {
            int x = a[i - 1], y = a[i];
            if (x < y) {
                if (x * 2 >= y) continue;
                ans += cnt(x, y);
            } else {
                if (y * 2 >= x) continue;
                ans += cnt(y, x);
            }
        }
        pr(ans);
    }
    return 0;
}
```

## Balanced Remainders

给一个长度为n的数组，n可以被3整除。  
可以执行操作n次，使得任意一个值+1。  
问最少操作多少次可以使得数组中%3==0/1/2的元素个数相等。

转化如下：  
给你$a,b,c$,保证$(a+b+c)\%3=0$  
有如下转化关系$a\rightarrow b\rightarrow c\rightarrow a$  
求最少转化多少次，使得$a=b=c$

我的模拟可以适应$\mod k$的情况

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
#define nxt(i) ((i + 1) % 3)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
int a[N], c[10];
int T, n, tar;
int main() {
    sc(T);
    while (T--) {
        memset(c, 0, sizeof c);
        sc(n);
        rep(i, 1, n) sc(a[i]), ++c[a[i] % 3];
        tar = n / 3;
        int mx = 0, mn = 0;
        rep(i, 1, 2) if (c[i] > c[mx]) mx = i;
        int ans = 0;
        while (c[0] != tar || c[1] != tar || c[2] != tar) {
            if (c[mx] < tar) {
                mx = nxt(mx);
                continue;
            }
            int d = (c[mx] - tar);
            ans += d;
            c[nxt(mx)] += d;
            c[mx] = tar;
            mx = nxt(mx);
        }
        pr(ans);
    }
    return 0;
}
```

## Sum of Cubes

判断一个数是否可以是两个正数的立方和。

这其实涉及到了[很厉害的数论问题](https://www.zhihu.com/question/40263900)，但其实暴力可过（

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
#define nxt(i) ((i + 1) % 3)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;

ll T, n;
bool chk(ll n) {
    ll tot = cbrt(n);
    rep(i, 1, tot) {
        ll a3 = 1LL * i * i * i;
        ll b3 = n - a3;
        ll t = cbrt(b3);
        if (t == 0) continue;
        if (t * t * t == b3) return 1;
    }
    return 0;
}
int main() {
    sc(T);
    while (T--) {
        sc(n);
        puts(chk(n) ? "YES" : "NO");
    }
    return 0;
}
```

## Permutation Transformation

按照如下规则将排列转化为一棵树，给出每个位置在树里的深度：

- 一个区间里，最大的数是根- 然后左右递归

所以直接模拟就可以了，我的写法和线段树有点像

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d ", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef int ll;
const int N = 105 + 7;
const int mod = 1e9 + 7;
int T, n;
int a[N], dep[N];
void dfs(int d, int L, int R) {
    int p = max_element(a + L, a + R + 1) - a;
    dep[p] = d;
    if (p > L) dfs(d + 1, L, p - 1);
    if (p < R) dfs(d + 1, p + 1, R);
}
int main() {
    sc(T);
    while (T--) {
        sc(n);
        rep(i, 1, n) sc(a[i]);
        dfs(0, 1, n);
        rep(i, 1, n) pr(dep[i]);
        putchar(10);
    }
    return 0;
}
```

## Accidental Victory

The championship consists of n−1 games, which are played according to the following rules:

- in each game, two random players with non-zero tokens are selected;

  - the player with more tokens is considered the winner of the game (in case of a tie, the winner is chosen randomly);

    - the winning player takes all of the loser's tokens;

      - n个人，每个人都有一定数量的代币。

        - 每轮比赛随机选两个拥有代币的玩家battle，赢家拿走输家的所有代币。

          - 如果两个人代币一样多，就随机产生赢家。

给定每个人的代币数量，求有哪些玩家有可能夺冠。

考虑贪心：

1. 首先可以考虑前缀和，比我少的，我都拿走。- 那现在我都拿走了，我的资产达到新高度了，现在比我少的，我再吸走。- 如果我最终可以比一开始最富的人还富，我就有机会夺冠。

逆向思维一下：

1. 如果一个人比我代币多，那么如果我可以夺冠，那他也一定可以。- 所以，如果初始代币少的人能夺冠，基准条件是，比他初始代币多的人一定可以夺冠。- 所以从后往前找，前缀和pre[i]<a[i]的点即可，这里断层。在这个点右边的所有玩家，都有机会赢。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d ", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
#define pii pair<int, int>
using namespace std;
typedef long long ll;
const int N = 2e5 + 7;
const int mod = 1e9 + 7;
pair<ll, int> a[N];
int n;
ll pre[N];
// inline int getLastLess(ll val) {
//     int p = upper_bound(a + 1, a + 1 + n, val) - a;
//     return p - 1;
// }
// bool chk(int i) {
//     //先吃掉所有比它小的
//     int nxt = i;
//     do {
//         i = nxt;
//         ll now = pre[i];
//         nxt = getLastLess(now);
//         if (nxt == n) return 1;
//     } while (nxt != i);
//     return 0;
// }
bool cmp(const pii &a, const pii &b) { return a.second < b.second; }
int main() {
    int T;
    sc(T);
    while (T--) {
        sc(n);
        rep(i, 1, n) scanf("%lld", &a[i].first), a[i].second = i;
        sort(a + 1, a + 1 + n);
        rep(i, 1, n) pre[i] = a[i].first + pre[i - 1];
        int ans = 1, tar = a[n].first;  //最多的一定有机会赢
        for (int i = n - 1; i; --i) {
            if (pre[i] >= a[i + 1].first) {
                ++ans;
                tar = a[i].first;
            } else
                break;
        }
        sort(a + 1, a + 1 + n, cmp);
        printf("%d\n", ans);
        rep(i, 1, n) if (a[i].first >= tar) pr(a[i].second);
        putchar(10);
    }
    return 0;
}
```

## Equalize the Array

波利卡普得到一个长度为n的数组a，如果存在一个数C，使数组中的每个数出现0或C次，则波利卡普认为数组很美。波利卡普想从数组a中去掉一些元素，使其美丽。

例如，如果n=6，a=[1,3,2,1,4,2]，那么以下选项可以使数组成为一个美丽的数组。

Polycarp删除位置2和5的元素 数组a等于[1,2,1,2];  
Polycarp删除位置1和6的元素，阵列a等于[3,2,1,4]。  
Polycarp删除位置1,2和6的元素，阵列a等于[2,1,4]。  
帮助Polycarp确定从数组a中删除的最少元素数，使其美观。

暴力即可。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 2e5 + 7;
const int mod = 1e9 + 7;
int a[N];
int main() {
    int T, n;
    sc(T);
    while (T--) {
        sc(n);
        map<int, int> mp;  // mp[x]表示数字x出现了多少次
        rep(i, 1, n) sc(a[i]), mp[a[i]]++;
        map<int, int, greater<int>> cnt;  // cnt[x]表示出现x次的数字有多少个
        for (auto i : mp) cnt[i.second]++;
        //其实就是希望最后留下的数字尽可能多
        //多的数字能删成少的，反之则不是
        int ans = 0, pre = 0;
        for (auto i : cnt) {
            int now = i.second;
            pre += now;
            ans = max(ans, pre * i.first);
        }
        pr(n - ans);
    }
    return 0;
}
```

## Old Floppy Drive

波利卡普在拆除阁楼时，发现阁楼上有一个旧的软盘驱动器。在驱动器中插入了一张圆形光盘，上面写着n个整数。

波利卡普把磁盘上的数字写进了一个数组。结果发现，驱动器的工作原理是按照以下算法进行的。

驱动器把一个正数x作为输入 然后把一个指针放到a阵列的第一个元素上；  
之后，驱动器开始旋转磁盘，每隔一秒就把指针移到下一个元素上，计算曾经被指过的所有元素的总和。由于磁盘是圆形的，所以在a阵列中，最后一个元素后面又是第一个元素。  
只要总和至少是x，硬盘就会关闭。  
波利卡普想了解更多关于硬盘的操作，但他完全没有空闲时间。所以他问了你m个问题。要回答其中的第i-th个问题，你需要找出如果你给它xi作为输入，驱动器将工作多少秒。请注意，在某些情况下，驱动器可以无限地工作。

例如，如果n=3,m=3，a=[1,-3,4]，x=[1,5,2]，那么问题的答案如下。

第一个问题的答案是0，因为驱动器最初指向的是第一项，初始和就是1。  
第二个查询的答案是6，驱动器将磁盘完全旋转两次，1+(-3)+4+1+(-3)+4+1=5。  
第三项查询的答案是2，答案为1+(-3)+4=2。

所以前缀和模拟就好了。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld ", (x))
#define rep(i, l, r) for (int i = (l); i <= (r); ++i)
using namespace std;
typedef long long ll;
const int N = 2e5 + 7;
const int mod = 1e9 + 7;
ll a[N], pre[N], n, m, x;
void solve() {
    ll sum = 0, mx = 0, ans = -1;
    rep(i, 1, n) {
        sum += a[i];
        mx = max(mx, sum);
        pre[i] = pre[i - 1] + a[i];
    }
    rep(i, 1, n) pre[i] = max(pre[i], pre[i - 1]);
    while (m--) {
        sc(x);
        if (mx < x && sum <= 0)  //永动机
            ans = -1;
        else if (mx >= x)  //会被峰值挡住
            ans = lower_bound(pre + 1, pre + 1 + n, x) - pre -
                  1;                        //第一个挡住的峰
        else {                              //衰竭
            ll p = (x - mx - 1) / sum + 1;  //轮次
            x -= p * sum;
            ans = lower_bound(pre + 1, pre + 1 + n, x) - pre - 1 + p * n;
        }
        pr(ans);
    }
}
int main() {
    ll T;
    sc(T);
    while (T--) {
        sc(n), sc(m);
        rep(i, 1, n) sc(a[i]);
        solve();
        putchar(10);
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/079fd1924c264cd596bd44096fd38404），发布于 2021-02-17。
