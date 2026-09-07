---
title: "Fool Problem"
date: 2020-05-12T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
斐波那契规律题。

只需要判断末尾是奇数还是偶数即可。

```
#include<bits/stdc++.h>
using namespace std;
char s[2025];
int main(){
    gets(s);
    int n=strlen(s);
    int a=s[n-1]-'0';
    if(a&1)cout<<-1<<endl;
    else cout<<1<<endl;
    return 0;
}
```

$$f(n)=\frac{1}{\sqrt{5}}((\frac{1+\sqrt{5}}{2})^n-(\frac{1-\sqrt{5}}{2})^n)$$

代入$f(n-1)f(n+1)-f(n)^2$得结果$(-1)^n$

我比赛的时候就是自己手动试了几个
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/121258dd8f564173b7156e2e90ae406c），发布于 2020-05-12。
