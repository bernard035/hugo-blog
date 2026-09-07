---
title: "Sequence 分治结构纳入线段树"
date: 2021-01-23T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "线段树"]
---
## 题意

给n个数，有两种操作

1. 把$a[x]$改成$y$
2. 求有多少个连续区间的最小值是$a[x]$

## 思路

首先很容易想到：求有多少个连续区间的最小值是$a[x]$，其实就是找到$a[x]$左边第一个比$a[x]$小的数，下标$L$，找到$a[x]$右边第一个比$a[x]$小的数，下标$R$，那么就有$(x-L)*(R-x)$个满足题意的区间。

然而硬找肯定是T的，虽然题目数据太水，稍微优化一下就能过。

考虑正解做法，明确目标：我们要找到找到$a[x]$左边第一个比$a[x]$小的数和$a[x]$右边第一个比$a[x]$小的数的下标。

那么可以用线段树**维护区间最小值**。

1. 随着区间范围的扩大，区间内的最小值只会变小不会变大，具备单调性：也就是说，可以二分。
2. 那么我们考虑将查找的区间的右端点固定为$x$，二分左端点，就可以找到$a[x]$左边第一个比$a[x]$小的数。右边同理。

于是本题解出，复杂度$O(n\log n \log n)$

## solution

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
ll a[N], tree[N << 2], n, op;
ll m, x, y, k;
#define ls (p << 1)
#define rs (p << 1 | 1)
#define mid (l + r >> 1)
void bulid(ll p = 1, ll l = 1, ll r = n) {
    if (l == r) {
        tree[p] = a[l];
        return;
    }
    bulid(ls, l, mid);
    bulid(rs, mid + 1, r);
    tree[p] = min(tree[ls], tree[rs]);
}
void upd(ll x, ll val, ll p = 1, ll l = 1, ll r = n) {//单点修改
    if (l == r) { 
        tree[p] = val;
        return;
    }
    if (x <= mid)
        upd(x, val, ls, l, mid);
    else
        upd(x, val, rs, mid + 1, r);
    tree[p] = min(tree[ls], tree[rs]);
}
ll query(ll x, ll y, ll p = 1, ll l = 1, ll r = n) {//区间查询
    if (x <= l && r <= y) return tree[p];
    ll res = LLONG_MAX;
    if (x <= mid) res = min(res, query(x, y, ls, l, mid));
    if (y > mid) res = min(res, query(x, y, rs, mid + 1, r));
    return res;
}
int main() {
    sc(n), sc(m);
    for (int i = 1; i <= n; ++i) sc(a[i]);
    bulid();
    while (m--) {
        sc(op);
        if (op & 1) {
            sc(x), sc(y);
            a[x] = y;
            upd(x, y);
        } else {
            sc(x);
            int L = 1, R = x, midv, L_id = x, R_id = x;
            while (L <= R) {
                midv = L + R >> 1;
                if (query(midv, x) == a[x])
                    L_id = midv, R = midv - 1;
                else
                    L = midv + 1;
            }
            L = x, R = n;
            while (L <= R) {
                midv = L + R >> 1;
                if (query(x, midv) == a[x])
                    R_id = midv, L = midv + 1;
                else
                    R = midv - 1;
            }
            pr(1LL * (x - L_id + 1) * (R_id - x + 1));
        }
    }
    return 0;
}
```

## 优化

由于是要找左边第一个比它小的，所以可以直接将二分的查找直接纳入线段树内：找区间内最右的小于$a[x]$的值。所以线段树的查找方式需要作出相应的调整：先搜索rson再搜索lson，如果搜到了直接返回。找右边第一个比他小的就是反过来。这样就是实现了将二分的搜索纳入了线段树里，从而实现了$O(n\log n)$的算法。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;

int tree[N << 2], a[N];
#define lson l, mid, rt << 1
#define rson mid + 1, r, rt << 1 | 1
inline int min(int x, int y) { return x < y ? x : y; }
inline void build(int l, int r, int rt) {
    if (l == r) {sc(tree[rt]);
        a[l] = tree[rt] ;
        return;
    }
    int mid = (l + r) >> 1;
    build(lson);
    build(rson);
    tree[rt] = min(tree[rt << 1], tree[rt << 1 | 1]);
}
inline void update(int x, int y, int l, int r, int rt) {
    if (l == r) {
        tree[rt] = y;
        return;
    }
    int mid = (l + r) >> 1;
    if (x <= mid)
        update(x, y, lson);
    else
        update(x, y, rson);
    tree[rt] = min(tree[rt << 1], tree[rt << 1 | 1]);
}
inline int lquery(int l, int r, int rt, int x, int y) {
    if (l == r) return l;
    int mid = (l + r) >> 1, ans = l;
    if (tree[rt << 1 | 1] < x && mid < y) {
        ans = lquery(rson, x, y);
        if (a[ans] < x)
            return ans;
        else
            ans = l;
    }
    if (tree[rt << 1] < x) ans = lquery(lson, x, y);
    return ans;
}
inline int rquery(int l, int r, int rt, int x, int y) {
    if (l == r) return l;
    int mid = (l + r) >> 1, ans = r;
    if (tree[rt << 1] < x && mid >= y) {
        ans = rquery(lson, x, y);
        if (a[ans] < x)
            return ans;
        else
            ans = r;
    }
    if (tree[rt << 1 | 1] < x) ans = rquery(rson, x, y);
    return ans;
}
int main() {
    ll op, x, y, n, m;
    sc(n), sc(m);
    ll l, r;
    build(1, n, 1);
    while (m--) {
        sc(op);
        if (op & 1) {
            sc(x), sc(y);
            update(x, y, 1, n, 1);
            a[x] = y;
        } else {
            sc(x);
            l = lquery(1, n, 1, a[x], x - 1);
            r = rquery(1, n, 1, a[x], x + 1);
            if (a[l] < a[x]) l += 1;
            if (a[r] < a[x]) r -= 1;
            printf("%lld\n", (x - l + 1) * (r - x + 1));
        }
    }
    return 0;
}
/*
5 2
1 2 6 3 4
2 3
2 2

5 2
4 3 2 5 6
2 3
2 2

10 4
6 21 2 24 19 1 14 8 22 18
1 6 15
1 5 20
1 9 12
2 7

*/
```

## 其他

由于本题是单点修改，区间查询，第一反应就是树状数组，也用树状数组实现了，可惜树状数组+二分的实现是$O(n\log n\log n\log n)$，因为树状数组维护的是区间最小值，所以单次查询$O(\log n\log n)$。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
#define lowbit(x) (x) & (-(x))
ll a[N], tree[N << 2], n, op;
ll m, x, y, k;
ll query(ll l, ll r) {
    ll ret = a[r];
    while (l <= r) {
        ret = min(ret, a[r]);
        for (--r; r - l >= lowbit(r); r -= lowbit(r)) {
            ret = min(ret, tree[r]);
        }
    }
    return ret;
}
void add(ll l) {
    tree[l] = a[l];
    for (ll i = 1; i < lowbit(l); i <<= 1) {
        tree[l] = min(tree[l], tree[l - i]);
    }
}
int main() {
    sc(n), sc(m);
    for (int i = 1; i <= n; ++i) sc(a[i]), add(i);

    // while (cin >> x >> y) {
    //     cout << query(x, y) << endl;
    // }
    while (m--) {
        sc(op);
        if (op & 1) {
            sc(x), sc(y);
            a[x] = y;
            add(x);
        } else {
            sc(x);
            int L = 1, R = x, midv, L_id = x, R_id = x;
            while (L <= R) {
                midv = L + R >> 1;
                if (query(midv, x) == a[x])
                    L_id = midv, R = midv - 1;
                else
                    L = midv + 1;
            }
            L = x, R = n;
            while (L <= R) {
                midv = L + R >> 1;
                if (query(x, midv) == a[x])
                    R_id = midv, L = midv + 1;
                else
                    R = midv - 1;
            }
       //     cout << L_id << ' ' << R_id << endl;
            pr(1LL * (x - L_id + 1) * (R_id - x + 1));
        }
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/267b56875ca148abb9e09db36213ceaa），发布于 2021-01-23。
