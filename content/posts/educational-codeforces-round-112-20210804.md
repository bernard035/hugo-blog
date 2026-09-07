---
title: "Educational Codeforces Round 112 (Rated for Div. 2)"
date: 2021-08-04T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "Codeforces"]
---
## A. PizzaForces

```
PS C:\Users\jiang> 8/20
0.4
PS C:\Users\jiang> 10/25
0.4
PS C:\Users\jiang> 6/15
0.4
PS C:\Users\jiang> 1/0.4
2.5
```

1. 无论选择订购何种披萨，其性价比都是一样的- 6/8/10能组成任意大于10的偶数

```
T = int(input())
for _ in range(T):
    n = int(input())
    if n <= 6: print(15)
    elif n <= 8: print(20)
    elif n <= 10: print(25)
    else:
        if n & 1: n += 1
        print(n // 2 * 5)
```

如果这道题没有找规律，而是寻求一般解法的话，将会考虑一个线性回归问题：在满足${6x+8y+10z\ge n}$的前提下，使得 ${15x*20y+25z}$ 尽可能小

## B. Two Tables

要放两张桌子，第一张桌子已经放下，给定空间大小，问第一张桌子的最小移动距离。

枚举四个方向是很容易想到的，但是归并处理，将代码简洁表示，才是重点。

```
T = int(input())
for _ in range(T):
    W, H = map(int, input().split())
    x1, y1, x2, y2 = map(int, input().split())
    w, h = map(int, input().split())
    ans = 10**8
    if x2 - x1 + w <= W:  # 水平方向
        ans = min(ans, max(0, w - x1))## 第二块放左边
        ans = min(ans, max(0, x2 - (W - w)))# 第二块放右边
    if y2 - y1 + h <= H:  # 竖直方向
        ans = min(ans, max(0, h - y1)) # 第二块放下边
        ans = min(ans, max(0, y2 - (H - h))) # 第二块放上边
    if ans == 10**8: ans = -1
    print(ans)
```

## C. Coin Rows

```
T = int(input())
for _ in range(T):
    n = int(input())
    a = []
    a.append(list(map(int, input().split())))
    a.append(list(map(int, input().split())))
    s0, s1 = sum(a[0]), sum(a[1])
    res = [s0 - a[0][0]]
    up, down = res[0], 0
    for i in range(1, n):
        up -= a[0][i]
        down += a[1][i - 1]
        res.append(max(up, down))
    print(min(res))
```

## D. Say No to Palindromes

1. 最终的串，对于其所有的${[kx,kx+2]}$子串，都是相同的一个abc的排列- 那么考虑枚举哪种abc的排列最合适即可- 考虑前缀和预处理`sum[3][3][N]` 表示 [何种字符][在每一节的位置][长度]，也就是说，`sum[i][j][k]`表示的是，在长度1-k里，在每一节的第j个位置里，有多少个字符i

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 2e5 + 7;
const int mod = 1e9 + 7;
char s[N];
int sum[3][3][N];
signed main() {
    int n, m;
    scanf("%d%d", &n, &m);
    scanf("%s", s + 1);
    rep(i, 1, n) {
        rep(j, 0, 2) rep(k, 0, 2) sum[j][k][i] = sum[j][k][i - 1];
        ++sum[s[i] - 'a'][i % 3][i];
    }
    int l, r;
    while (m--) {
        scanf("%d%d", &l, &r);
        int p[] = {0, 1, 2};
        int ans = INT_MAX;
        do {
            int t = 0;
            for (int i = 0; i < 3 && i <= r - l; ++i) {
                int *su = sum[p[i]][(l + i) % 3];
                t += 1 + (r - l - i) / 3 - (su[r] - su[l - 1]);
            }
            ans = min(ans, t);
        } while (next_permutation(p, p + 3));
        printf("%d\n", ans);
    }
    return 0;
}
```

## E. Boring Segments

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
//线段树维护区间最小值 区间加
using namespace std;
typedef int ll;
const int N = 1e6 + 7;
const ll mod = 1e9 + 7;
ll a[N], tree[N << 2], lazy[N << 2], n;
#define ls (p << 1)
#define rs (p << 1 | 1)
#define mid (l + r >> 1)
void bulid(ll p = 1, int l = 1, int r = n) {
    lazy[p] = 0;
    if (l == r) {
        tree[p] = a[l];
        return;
    }
    bulid(ls, l, mid);
    bulid(rs, mid + 1, r);
    tree[p] = min(tree[ls], tree[rs]);
}
inline void PushDown(int p) {
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
ll query(int x = 1, int y = n, int p = 1, int l = 1, int r = n) {
    if (x <= l && r <= y) return tree[p];
    if (lazy[p]) PushDown(p);
    if (y <= mid) return query(x, y, ls, l, mid);
    if (x > mid) return query(x, y, rs, mid + 1, r);
    return min(query(x, y, ls, l, mid), query(x, y, rs, mid + 1, r));
}
struct node {
    int l, r, w;
    bool operator<(const node &o) const { return w < o.w; }
};
struct cmp {
    bool operator()(const int &x, const int &y) const { return x > y; }
};
int main() {
    int sz, m;
    sc(sz), sc(m);
    n = m - 1;
    vector<node> s(sz);
    for (node &x : s) {
        scanf("%d%d%d", &x.l, &x.r, &x.w);
        --x.r;
    }
    sort(s.begin(), s.end());
    int d = INT_MAX;
    map<int, int> mn;
    map<int, int, cmp> mx;
    for (int L = 0, R = 0; R < sz; ++R) {
        add(s[R].l, s[R].r);
        ++mn[s[R].w], ++mx[s[R].w];
        while (tree[1] > 0 && L <= R) {
            d = min((*mx.begin()).first - (*mn.begin()).first, d);
            add(s[L].l, s[L].r, -1);
            --mn[s[L].w], --mx[s[L].w];
            if (!mn[s[L].w]) mn.erase(s[L].w);
            if (!mx[s[L].w]) mx.erase(s[L].w);
            ++L;
        }
    }
    pr(d);
    return 0;
}
```

不用维护最值，因为已经排序

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%d\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
//线段树维护区间最小值 区间加
using namespace std;
typedef int ll;
const int N = 1e6 + 7;
const ll mod = 1e9 + 7;
ll a[N], tree[N << 2], lazy[N << 2], n;
#define ls (p << 1)
#define rs (p << 1 | 1)
#define mid (l + r >> 1)
void bulid(ll p = 1, int l = 1, int r = n) {
    lazy[p] = 0;
    if (l == r) {
        tree[p] = a[l];
        return;
    }
    bulid(ls, l, mid);
    bulid(rs, mid + 1, r);
    tree[p] = min(tree[ls], tree[rs]);
}
inline void PushDown(int p) {
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
ll query(int x = 1, int y = n, int p = 1, int l = 1, int r = n) {
    if (x <= l && r <= y) return tree[p];
    if (lazy[p]) PushDown(p);
    if (y <= mid) return query(x, y, ls, l, mid);
    if (x > mid) return query(x, y, rs, mid + 1, r);
    return min(query(x, y, ls, l, mid), query(x, y, rs, mid + 1, r));
}
struct node {
    int l, r, w;
    bool operator<(const node &o) const { return w < o.w; }
};
struct cmp {
    bool operator()(const int &x, const int &y) const { return x > y; }
};
int main() {
    int sz, m;
    sc(sz), sc(m);
    n = m - 1;
    vector<node> s(sz);
    for (node &x : s) {
        scanf("%d%d%d", &x.l, &x.r, &x.w);
        --x.r;
    }
    sort(s.begin(), s.end());
    int d = INT_MAX;
    for (int L = 0, R = 0; R < sz; ++R) {
        add(s[R].l, s[R].r);
        while (tree[1] > 0 && L <= R) {
            d = min(s[R].w-s[L].w, d);
            add(s[L].l, s[L].r, -1);
            ++L;
        }
    }
    pr(d);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/8723363ed5064e8f8bb1f70a5af038ac），发布于 2021-08-04。
