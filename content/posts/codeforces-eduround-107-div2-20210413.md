---
title: "codeforces eduRound 107 div2 A-D"
date: 2021-04-13T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题集"]
tags: ["题集"]
---
<https://codeforces.com/contest/1511>

## A

有两个服务器。  
你是一个电影导演，你导演了电影。  
喜欢的人会点赞，不喜欢的人会点踩。  
而对于第三种人，如果他看到的踩比赞多，他就会点踩，否则点赞。  
问最多可以收到多少个赞。

比较简单的贪心。

```
#include <bits/stdc++.h>
using namespace std;
int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        int n, a;
        scanf("%d", &n);
        int ans = 0;
        while (n--) scanf("%d", &a), ans += (a != 2);
        printf("%d\n", ans);
    }
    return 0;
}
```

## B

给定$a,b,c$三个数的十进制下的位数$len(a),len(b),len(c)$，你需要构造$\gcd (a,b)=c$

置位的思路是最好的。

```
#include <bits/stdc++.h>
using namespace std;
int f[10] = {0, 1};
int main() {
    for (int i = 2; i < 10; ++i) f[i] = f[i - 1] * 10;
    int T;
    scanf("%d", &T);
    while (T--) {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        a = f[a], b = f[b];
        if (b != c) b += f[c];
        printf("%d %d\n", a, b);
    }
    return 0;
}
```

## C

模拟即可。deque模拟1s都不要。  
我的思路是：维护每个颜色最顶上的牌即可。因为只有它们会参与移动。  
底层并没有跃迁的机会。

## D

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 2e5 + 7;
char s[N];
int main() {
    int n, k;
    sc(n), sc(k);
    int p = 0;
    for (char i = 'a'; i < 'a' + k; ++i) {
        s[++p] = i;
        for (char j = 1 + i; j < 'a' + k; ++j) {
            s[++p] = i;
            s[++p] = j;
        }
    }
    int i = 1;
    while (n--) {
        if (i > p) i = 1;
        putchar(s[i++]);
    }
    putchar(10);
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/024ddeaf738040fb9261115e9be2df1b），发布于 2021-04-13。
