---
title: "Sumo and Balloon"
date: 2020-06-06T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
## 题意

三维空间有一个点，它以每秒增加$L$单位体积的速率不断膨胀，问什么时候能碰到一个给定的面。

## 思路

本题在高数大佬+室友+队友的帮助下采用**向量**解决。

题目给定的是三个点来确定一个面。那么由三个点可以得到两个向量$\alpha\ \beta$:

```
a1=x2-x1;a2=y2-y1;a3=z2-z1
b1=x3-x1;b2=y3-y1;b3=z3-z1
```

**由这两个向量的叉积可以得到平面的方程**：

```
a=a2*b3-a3*b2
b=a3*b1-a1*b3
c=a1*b2-a2*b1
d=-(a*x1+b*y1+c*z1)
```

然后根据点到平面的距离公式

$$\Large d=\frac{|Ax+By+Cz+D|}{\sqrt{A^2+B^2+C^2}}$$

就可以求出球心到平面的距离（直径）

由球的体积公式$V=\frac{4}{3}\pi r^3=tL$可得所需要的$t$

如果没听懂去文末复习高数

## solution

```
import math
L=int(input())
x,y,z=map(int,input().split())
x1,y1,z1=map(int,input().split())
x2,y2,z2=map(int,input().split())
x3,y3,z3=map(int,input().split())
a1=x2-x1;a2=y2-y1;a3=z2-z1
b1=x3-x1;b2=y3-y1;b3=z3-z1
a=a2*b3-a3*b2
b=a3*b1-a1*b3
c=a1*b2-a2*b1
d=-(a*x1+b*y1+c*z1)
R=(abs(a*x+b*y+c*z+d)/2)/math.sqrt(a*a+b*b+c*c)
t=4/3*R*R*R*math.pi/L
print(t)
```

## 高数

![图片说明](/images/a9903a08494586b6.jpg)

![图片说明](/images/2229df2788be10d1.jpg)
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/9a9130d5d20d44b09cd35d9b8ddc2cf6），发布于 2020-06-06。
