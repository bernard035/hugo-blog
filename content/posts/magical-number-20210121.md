---
title: "Magical Number 暴力出奇迹"
date: 2021-01-21T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
magical number是越走越窄的，在$10^{25}$就已经结束了。

所以直接dfs即可，只是不敢写。

最大的可行魔法数消耗木棍139，故可打表。

然而根本不需要打表，就硬搜就能过。

## 打表代码

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
using namespace std;
typedef __int128 ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
ll ans[1005];
int stick[10] = {6, 2, 5, 5, 4, 5, 6, 3, 7, 6};
inline int dig(ll val) {
    int res = 0;
    while (val) {
        res += stick[val % 10];
        val /= 10;
    }
    return res;
}
void dfs(int len, ll val) {
    for (int i = 0; i < 10; ++i) {
        ll now = val * 10 + i;
        if (now % (len + 1) == 0) {  // now is magic
            int cost = dig(now);
            if (ans[cost] < now) ans[cost] = now;
            dfs(len + 1, now);
        }
    }
}
inline void print(__int128 x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
int main() {
    for (int i = 1; i <= 9; ++i) {
        int cost = dig(i);
        if (ans[cost] < i) ans[cost] = i;
        dfs(1, i);
    }
    freopen("tb.txt", "w", stdout);
    for (int i = 0; i <= 140; ++i) print(ans[i]), putchar(',');
}
```

## ac代码

```
a=[0,0,1,7,4,5,14,74,141,741,747,744,444,1412,7412,7472,7476,7440,14125,74125,74725,74765,74760,141654,741654,1416541,7412041,7472521,7412524,7444507,7472045,14165416,74120416,74725216,74445072,74765464,141252327,741204162,747252162,744450723,747654642,747600723,1412523270,7412041620,7472521620,7444507230,14125232704,74720456107,74120416205,74725216203,74405456103,76245056107,747204561072,141252327048,7444563210721,747654642024,6212045610121,1836541620121,7412524830724,7472584890121,74445632107210,7624505610722,12365432101232,74725848901214,92160672307214,744456321072105,76240224907210,76245056107220,183654162012165,141204726084525,747252162036945,7444563210721056,744450723060240,948402729012765,984402324072720,849252645012240,7472521620369456,7624022490721056,7444507230602400,7292040840721056,74445072306024001,72645656402410567,62165464207210569,76240224907210569,96640256702472001,144804807072945612,726456564024105672,747252162036945684,726054723024825672,966402567024720012,7264565640241056724,6216546420721056967,9664025670247200127,4412527200484208287,7624022490721056965,7052521680607200301,72645656402410567240,8672524020602400427,9812523240364656789,8468047260244656728,9488521620604656185,46240816501236007840,84000072607234561840,726456564024105672408,9248526450969456969,84680472602446567280,40525240503678082220,74440800907200000020,7264565640241056724082,96600072602418082260,84045016803678089480,82525248003646560660,663252885024660810201,8468047260244656728072,72645656402410567240820,1444086450482256366038,12360600901222567200901,14440864504822563660381,5672520000485856186064,3420060030244208700096,2408588820001056602074,4028521680729008280092,966858966048360042609,144408645048225636603816,0,0,24085888200010566020746,0,9668589660483600426096,402852168072900828009216,0,0,0,0,360852885036840078603672,0,0,0,0,3608528850368400786036725,0]
n=int(input())
if n>=1000 or a[n]==0:print(-1)
else:print(a[n])
```

## py暴力

```
stick = [6, 2, 5, 5, 4, 5, 6, 3, 7, 6]
ans = -1

def getcost(val):
    res = 0
    while val:
        res += stick[val % 10]
        val //= 10
    return res

def dfs(len, val, n):
    for i in range(10):
        now = val*10+i
        cost = getcost(now)
        if now % (len+1) == 0 and cost <= n:
            if cost == n:
                global ans
                ans = max(ans, now)
            dfs(len+1, now, n)

n = int(input())

if n < 140:
    for i in range(1, 10):
        if stick[i] == n:
            ans = max(ans, i)
        dfs(1, i, n)

print(ans)
```

## py优化DFS

受#undefine启发，大幅优化

```
stick = [6, 2, 5, 5, 4, 5, 6, 3, 7, 6]
ans = -1

def dfs(len, val, n):
    for i in range(10):
        now = val*10+i
        if now % (len+1) == 0:
            if n-stick[i] == 0:
                global ans
                ans = max(ans, now)
            if n-stick[i] > 0:
                dfs(len+1, now, n-stick[i])

n = int(input())
for i in range(1, 10):
    if stick[i] == n:
        ans = max(ans, i)
    dfs(1, i, n-stick[i])
print(ans)
```

## 出题人的话

![图片说明](/images/c191bd7393359ad9.png "图片标题") ![图片说明](/images/4741fe758966b67e.png "图片标题") ![图片说明](/images/0f292e4a3c57bdd6.png "图片标题")

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
using namespace std;
typedef __int128 ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
ll ans[1005];
int M[N];
int stick[10] = {6, 2, 5, 5, 4, 5, 6, 3, 7, 6};
inline int dig(ll val) {
    int res = 0;
    while (val) {
        res += stick[val % 10];
        val /= 10;
    }
    return res;
}
void dfs(int len, ll val) {
    for (int i = 0; i < 10; ++i) {
        ll now = val * 10 + i;
        if (now % (len + 1) == 0) {  // now is magic
            ++M[len + 1];
            int cost = dig(now);
            if (ans[cost] < now) ans[cost] = now;
            dfs(len + 1, now);
        }
    }
}
inline void print(__int128 x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) print(x / 10);
    putchar(x % 10 + '0');
}
int main() {
    for (int i = 1; i <= 9; ++i) {
        int cost = dig(i);
        if (ans[cost] < i) ans[cost] = i;
        ++M[1];
        dfs(1, i);
    }
    for (int i = 1; i <= 25; ++i) cout << M[i] << endl;
}
```

![图片说明](/images/ec5396f16e3ecb4a.png "图片标题")
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/8660b50b5b7e4a2f8e1b7d68ed81b55a），发布于 2021-01-21。
