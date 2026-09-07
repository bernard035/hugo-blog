---
title: "Codeforces Global Round 14"
date: 2021-05-03T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题集"]
tags: ["题集", "Codeforces"]
---
## A

如果会达到爆炸态，向后交换以推迟即可。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld ", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;

int main() {
    ll T, n, x, d;
    sc(T);
    while (T--) {
        sc(n), sc(x);
        ll s = 0;
        vector<ll> a;
        rep(i, 1, n) sc(d), a.push_back(d), s += d;
        if (s == x)
            puts("NO");
        else {
            puts("YES");
            ll sum = 0;
            for (int i = 0; i < a.size(); ++i) {
                sum += a[i];
                if (sum == x)
                    if (i != a.size() - 1)
                        sum += a[i + 1] - a[i], swap(a[i], a[i + 1]);
            }
            for (ll val : a) pr(val);
            puts("");
        }
    }
    return 0;
}
```

## B

最小的方形（微元）只有2种状态：

1. 两个三角的斜边拼接- 4个三角的直角边拼接

故只需要n/2或者n/4是完全平方数即可

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
bool isSqrt(ll n) {
    ll x = sqrt(n);
    return x * x == n;
}
int main() {
    int T;
    scanf("%d", &T);
    ll n;
    while (T--) {
        scanf("%lld", &n);
        if (n % 2 == 0 and isSqrt(n / 2))
            puts("YES");
        else if (n % 4 == 0 and isSqrt(n / 4))
            puts("YES");
        else
            puts("NO");
    }
    return 0;
}
```

## C

优先选择当前最矮的栈去放方块。

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%d ", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
struct node {
    ll val;
    int id;
    int to;
} a[N];
int T, n, m, x;
int main() {
    scanf("%d", &T);
    while (T--) {
        scanf("%d%d%d", &n, &m, &x);
        rep(i, 1, n) {
            sc(a[i].val);
            a[i].id = i;
        }
        if (n < m) {
            puts("NO");
            continue;
        }
        sort(a + 1, a + 1 + n, [&](node x, node y) { return x.val > y.val; });
        int p = 0, f = 1;
        multimap<ll, int> mp;
        rep(i, 1, m) {
            mp.insert(make_pair(a[i].val, i));
            a[i].to = i;
        }
        rep(i, m + 1, n) {
            pair<ll, int> top = *mp.begin();
            top.first += a[i].val;
            mp.erase(mp.begin());
            a[i].to = top.second;
            mp.insert(top);
        }
        ll mn = (*mp.begin()).first, mx = mn;
        for (auto v : mp) mx = max(v.first, mx);
        if (mx - mn > x) {
            puts("NO");
            continue;
        }
        puts("YES");
        sort(a + 1, a + 1 + n, [&](node x, node y) { return x.id < y.id; });
        rep(i, 1, n) pr(a[i].to);
        putchar(10);
    }
    return 0;
}
```

## D

1. 先把能凑的左右凑出来- 再把多的一边里面，能通过换边，凑出一对的，换边。直到左右两边数目一致- 调整单边颜色即可

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 2e5 + 7;
const int mod = 1e9 + 7;
int a[N];
int main() {
    int T, n, left, right;
    sc(T);
    while (T--) {
        sc(n), sc(left), sc(right);
        rep(i, 1, n) sc(a[i]);
        map<int, int> l, r;
        rep(i, 1, left) l[a[i]] += 1;

        rep(i, left + 1, n) {  // 去重
            if (l.count(a[i])) {
                l[a[i]] -= 1;
                left -= 1, right -= 1;
                if (l[a[i]] == 0) l.erase(a[i]);
            } else
                r[a[i]] += 1;
        }

        // for (auto p : l) cout << "L " << p.first << ' ' << p.second << '\n';
        // for (auto p : r) cout << "R " << p.first << ' ' << p.second << '\n';

        ll cost = 0;

        // 自我消除
        if (left > right)
            for (map<int, int>::iterator i = l.begin(); i != l.end(); ++i) {
                int &cnt = (*i).second;
                if (cnt >= 2) {
                    if (left - (cnt - cnt % 2) >= right) {
                        left -= cnt - cnt % 2;
                        cost += cnt / 2;
                        cnt %= 2;
                    } else {
                        ll d = left - right;
                        cost += d / 2;
                        left -= d;
                        cnt -= d * 2;
                        break;
                    }
                }
                if (left == right) break;
            }
        else if (right > left)
            for (map<int, int>::iterator i = r.begin(); i != r.end(); ++i) {
                int &cnt = (*i).second;
                if (cnt >= 2) {
                    if (right - (cnt - cnt % 2) >= left) {
                        right -= cnt - cnt % 2;
                        cost += cnt / 2;
                        cnt %= 2;
                    } else {
                        ll d = right - left;
                        cost += d / 2;
                        right -= d;
                        cnt -= d * 2;
                        break;
                    }
                }
                if (left == right) break;
            }

        ll lcnt = 0, rcnt = 0;
        for (auto p : l) lcnt += p.second;
        for (auto p : r) rcnt += p.second;
        cerr << lcnt << ' ' << rcnt << '\n';
        cost += min(lcnt, rcnt);
        ll d = abs(lcnt - rcnt);
        cost += d;
        pr(cost);
    }
    return 0;
}
```

## E

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
int n, mod;
ll pow2[401], dp[401][401], comb[401][401];
signed main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    cin >> n >> mod;
    pow2[0] = 1;
    rep(i, 1, n) pow2[i] = pow2[i - 1] * 2 % mod;
    rep(i, 0, n) {  // Pascal三角组合数
        comb[i][0] = comb[i][i] = 1;
        for (int j = 1; j < i; j++)
            comb[i][j] = (comb[i - 1][j] + comb[i - 1][j - 1]) % mod;
    }
    rep(i, 1, n) {
        dp[i][i] = pow2[i - 1];
        for (int j = i / 2 + 1; j < i; ++j) {
            for (int k = i - 1; k > 1 && j - i + k > 0; --k) {
                dp[i][j] = (dp[i][j] + dp[k - 1][j - i + k] * comb[j][i - k] %
                                           mod * pow2[i - k - 1]) %
                           mod;
            }
        }
    }
    ll ans = 0;
    for (int i = 1; i <= n; i++) ans = (ans + dp[n][i]) % mod;
    cout << ans;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/9ddecdf3cd764fe7bc1dd690f6b5c5f9），发布于 2021-05-03。
