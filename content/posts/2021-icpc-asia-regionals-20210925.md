---
title: "2021 ICPC 网络赛第二场 Asia Regionals Online Contest (II)"
date: 2021-09-25T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/赛后分析"]
tags: ["赛后分析", "ICPC"]
---
线段树最后debug时间不够了orz 这场打得一般

把代码放上来

## G

![图片说明](/images/492425b2ec7fabb7.png "图片标题")

泰勒展开$\LARGE ln(1+x)=x-\frac{x^2}{2}+\frac{x^3}{3}-\frac{x^4}{4}+\dots+(-1)^{n-1}\frac{x^n}{n}+o(x^n)\\ ~\\$

```
#include <bits/stdc++.h>
using namespace std;
const int N = 1e5 + 7;
typedef long long ll;
const double eps = 1e-8;
#define int ll
#define rep(i, l, r) for (int i = l; i <= r; ++i)
ll a[N], b[N], n, t;
signed main() {
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> t;
    int suma = 0, sumb = 1;
    rep(i, 1, n) cin >> a[i] >> b[i];
    if (t == 0)  return cout << 0, 0;
    for (int i = 1; i < t; ++i) {
        ll sum = 0;
        for (int j = 1; j <= n; ++j) {
            sum += a[j] * pow(b[j], i);
        }
        if (sum) return cout << "infinity", 0;
    }
    for (int i = 1; i <= n; ++i) {
        if (t % 2 == 0) a[i] *= -1;
        for (int j = 1; j <= t; ++j) {
            a[i] *= b[i];
        }
        suma += a[i];
    }
    int gcd = __gcd(t, abs(suma));
    t /= gcd, suma /= gcd;

    if (t == 1)
        cout << suma;
    else
        cout << suma << '/' << t;
    return 0;
}
```

## J

![图片说明](/images/3887beebca0fde0d.png "图片标题")

队友用桶写的

```
#include <bits/stdc++.h>
using namespace std;
const int N = 507;
typedef long long ll;
// #define int ll
#define rep(i, l, r) for (int i = l; i <= r; ++i)
double c[N][N];
int a[N][N], n;
int dx[] = {1, -1, 0, 0};
int dy[] = {0, 0, -1, 1};
bool out(int x, int y) { return x >= 1 and x <= n and y >= 1 and y <= n; }
vector<pair<int, int>> mn[10000 + 1];
signed main() {
    double m;
    scanf("%d%lf", &n, &m);
    int maxht = 0, minht = 10000;
    rep(i, 1, n) rep(j, 1, n) scanf("%d", &a[i][j]),
        maxht = max(maxht, a[i][j]), minht = min(minht, a[i][j]);

    rep(i, 1, n) rep(j, 1, n) {
        c[i][j] = m;
        mn[a[i][j]].push_back({i, j});
    }

    for (int i = maxht; i > minht; --i) {
        for (auto k : mn[i]) {
            int x = k.first, y = k.second;
            int cnt = 0;
            bool u = 0, d = 0, l = 0, r = 0;
            if (a[x][y] > a[x][y - 1] and y - 1 >= 1) {
                cnt++;
                u = 1;
            }
            if (a[x][y] > a[x][y + 1] and y + 1 <= n) {
                cnt++;
                d = 1;
            }
            if (a[x][y] > a[x - 1][y] and x - 1 >= 1) {
                cnt++;
                l = 1;
            }
            if (a[x][y] > a[x + 1][y] and x + 1 <= n) {
                cnt++;
                r = 1;
            }
            if (u) c[x][y - 1] += c[x][y] / cnt;
            if (d) c[x][y + 1] += c[x][y] / cnt;
            if (l) c[x - 1][y] += c[x][y] / cnt;
            if (r) c[x + 1][y] += c[x][y] / cnt;
            c[x][y] = 0;
        }
    }

    rep(i, 1, n) {
        rep(j, 1, n) printf("%.10lf ", c[i][j]);
        putchar(10);
    }
    return 0;
}
```

赛后补了一个稍微好看点的

```
#include <bits/stdc++.h>
using namespace std;
const int N = 507;
typedef long long ll;
// #define int ll
#define rep(i, l, r) for (int i = l; i <= r; ++i)
double c[N][N];
int a[N][N], n;
int dx[] = {1, -1, 0, 0};
int dy[] = {0, 0, -1, 1};
bool ins(int x, int y) { return x >= 1 and x <= n and y >= 1 and y <= n; }
vector<pair<int, int>> mn[10000 + 1];
signed main() {
    double m;
    scanf("%d%lf", &n, &m);
    int maxht = 0, minht = 10000;
    rep(i, 1, n) rep(j, 1, n) scanf("%d", &a[i][j]),
        maxht = max(maxht, a[i][j]), minht = min(minht, a[i][j]);

    rep(i, 1, n) rep(j, 1, n) {
        c[i][j] = m;
        mn[a[i][j]].emplace_back(i, j);
    }

    for (int i = maxht; i > minht; --i) {
        for (auto p : mn[i]) {
            int x = p.first, y = p.second, cnt = 0;
            rep(k, 0, 3) {
                int xx = x + dx[k], yy = y + dy[k];
                if (ins(xx, yy) && a[x][y] > a[xx][yy]) ++cnt;
            }
            rep(k, 0, 3) {
                int xx = x + dx[k], yy = y + dy[k];
                if (ins(xx, yy) && a[x][y] > a[xx][yy])
                    c[xx][yy] += c[x][y] / cnt;
            }
            c[x][y] = 0;
        }
    }

    rep(i, 1, n) {
        rep(j, 1, n) printf("%.10lf ", c[i][j]);
        putchar(10);
    }
    return 0;
}
```

## M

![图片说明](/images/a405fd93f64e5390.png "图片标题")

```
#include <bits/stdc++.h>
using namespace std;
const int N = 105;
typedef long long ll;
#define int ll
#define rep(i, l, r) for (int i = l; i <= r; ++i)
ll a[N], b[N], n, sg[N], c[N];
void opt(ll c[]) {
    for (int i = 0; i < n; ++i) {
        printf("%lld", c[i]);
        if (i != n - 1) putchar(' ');
    }
}
signed main() {
    cin >> n;
    for (int i = 0; i < n; ++i) cin >> sg[i];
    for (int i = 0; i < n; ++i) cin >> a[i];
    for (int i = 0; i < n; ++i) cin >> b[i];

    for (int i = 0; i < n; ++i) {
        c[i] += a[i] + b[i];
        while (c[i] >= 2) {
            c[i] -= 2;
            for (int j = i + 1; j < n; ++j) {
                c[j] += 1;
                if (sg[j] == sg[i]) break;
            }
        }
    }
    opt(c);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/bdade2a98ede4af2b6065f8896835a7f），发布于 2021-09-25。
