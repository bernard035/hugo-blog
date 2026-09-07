---
title: "线段树 区间加 区间查 板子"
date: 2020-12-07T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "线段树"]
---
```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
ll a[N], tree[N << 2], lazy[N << 2];
ll n, m, op, x, y, k;
#define ls (p << 1)
#define rs (p << 1 | 1)
#define mid (l + r >> 1)
void bulid(ll p = 1, ll l = 1, ll r = n) {
    lazy[p] = 0;
    if (l == r) {
        tree[p] = a[l];
        return;
    }
    bulid(ls, l, mid);
    bulid(rs, mid + 1, r);
    tree[p] = tree[ls] + tree[rs];
}
inline void PushDown(ll p, ll tot) {
    lazy[ls] += lazy[p];
    lazy[rs] += lazy[p];
    tree[ls] += lazy[p] * (tot - tot / 2);
    tree[rs] += lazy[p] * (tot / 2);
    lazy[p] = 0;
}
void add(ll x, ll y, ll val, ll p = 1, ll l = 1, ll r = n) {
    if (x <= l && r <= y) {  //当前区间可被所求区间覆盖
        lazy[p] += val;
        tree[p] += val * ((ll)(r - l + 1));
        return;
    }
    PushDown(p, r - l + 1);
    if (x <= mid) add(x, y, val, ls, l, mid);
    if (y > mid) add(x, y, val, rs, mid + 1, r);
    tree[p] = tree[ls] + tree[rs];
}
ll query(ll x, ll y, ll p = 1, ll l = 1, ll r = n) {
    if (x <= l && r <= y) return tree[p];
    ll res = 0;
    if (lazy[p]) PushDown(p, r - l + 1);
    if (x <= mid) res += query(x, y, ls, l, mid);
    if (y > mid) res += query(x, y, rs, mid + 1, r);
    return res;
}
int main() {
    sc(n), sc(m);
    for (int i = 1; i <= n; ++i) sc(a[i]);
    bulid();
    while (m--) {
        sc(op);
        if (op & 1) {
            sc(x), sc(y), sc(k);
            add(x, y, k);
        } else {
            sc(x), sc(y);
            pr(query(x, y));
        }
    }
    return 0;
}
```

改成区间查询最小值

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
//线段树维护区间最小值 区间加
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
ll a[N], tree[N << 2], lazy[N << 2], n;
ll m, op, x, y, k;
#define ls (p << 1)
#define rs (p << 1 | 1)
#define mid (l + r >> 1)
void bulid(ll p = 1, ll l = 1, ll r = n) {
    lazy[p] = 0;
    if (l == r) {
        tree[p] = a[l];
        return;
    }
    bulid(ls, l, mid);
    bulid(rs, mid + 1, r);
    tree[p] = min(tree[ls], tree[rs]);
}
inline void PushDown(ll p) {
    if (lazy[p]) {
        lazy[ls] += lazy[p];
        lazy[rs] += lazy[p];
        tree[ls] += lazy[p];
        tree[rs] += lazy[p];
        lazy[p] = 0;
    }
}
void add(int x, int y, ll val = 1, int p = 1, int l = 1, int r = n) {
    if (x <= l && r <= y) {  //当前区间可被所求区间覆盖
        lazy[p] += val;
        tree[p] += val;
        return;
    }
    PushDown(p);
    if (x <= mid) add(x, y, val, ls, l, mid);
    if (y > mid) add(x, y, val, rs, mid + 1, r);
    tree[p] = min(tree[ls], tree[rs]);
}
ll query(ll x, ll y, int p = 1, int l = 1, int r = n) {
    if (x <= l && r <= y) return tree[p];
    if (lazy[p]) PushDown(p);
    if (y <= mid) return query(x, y, ls, l, mid);
    if (x > mid) return query(x, y, rs, mid + 1, r);
    return min(query(x, y, ls, l, mid), query(x, y, rs, mid + 1, r));
}
int main() {
    sc(n), sc(m);
    // for (int i = 1; i <= n; ++i) sc(a[i]);
    bulid();
    while (m--) {
        sc(op);
        if (op & 1) {
            sc(x), sc(y), sc(k);
            add(x, y, k);
        } else {
            sc(x), sc(y);
            pr(query(x, y));
        }
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/e75b69c997bd415a86f61c42b505ac0d），发布于 2020-12-07。
