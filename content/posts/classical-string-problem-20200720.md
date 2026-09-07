---
title: "Classical String Problem 第三场B"
date: 2020-07-20T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
## 题意

给一个字符串，每次把头几个字母挪到尾部，或者把尾部几个字母挪到头部。随时会问现在这个字符串的第几个字符是什么。

## 思路

仔细观察，发现无论怎样移动，字符串的相对顺序没有发生改变。也就是说，这是一个循环队列，一个转轮，在一直转，只是那个指向队头的位置在变而已。

![图片说明](/images/0669ddd0de0bb5c2.png)

所以采用游标管理。

最后注意数据吞吐量大，不要用cin、cout。以及格式需要getchar。

## code

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
using namespace std;
typedef long long ll;
const int N = 2e6 + 7;
char s[N];
int main() {
    scanf("%s", s);
    int T, x, len = strlen(s), base = 0;
    sc(T);
    char op;
    while (T--) {
        getchar();
        scanf("%c%d", &op, &x);
        if (op == 'M')
            base = (base + len + x) % len;
        else
            putchar(s[(x + base + len - 1) % len]), putchar('\n');
    }
    return 0;
}
```

比赛的时候写得冗余一点：

```
#include <bits/stdc++.h>
#define sc(x) scanf("%d", &(x))
using namespace std;
typedef long long ll;
const int N = 2e6 + 7;
char s[N];
int main() {
    scanf("%s", s);
    int T, x, len = strlen(s), base = 0;
    sc(T);
    char op;
    while (T--) {
        getchar();
        scanf("%c%d", &op, &x);
        if (op == 'M') {
            if (x > 0) {
                base += x;
            } else {
                base += len;
                base += x;
                base %= len;
            }
        } else {
            int ans = x + base + len - 1;
            ans %= len;
            putchar(s[ans]);
            putchar('\n');
        }
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/edf1293f263349cca9aa6ad5b7834c90），发布于 2020-07-20。
