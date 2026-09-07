---
title: "第三届中国计量大学ACM程序设计竞赛-python"
date: 2020-06-05T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
## [Arithmetic Problems](https://ac.nowcoder.com/acm/contest/5795/A)

### 题意

![图片说明](/images/16b898219badbf97.png "图片标题")

给你一个式子，减号的意思是乘号，除号的意思是加号，加号的意思是除号，乘号的意思是减号。

要求计算这个式子。

## 思路

使用`eval`函数直接计算。

但是在此之前，要完成符号的转换：要注意，不能直接把减号变成乘号，否则会发生替换的覆盖，应该先转换成中间变量，再逐一转换成目标符号。

另外由于表达式过长，**必须提高默认的递归栈深**。

## solution

```
import sys
sys.setrecursionlimit(600000)
s=input()
s=s.replace(' ','')
s=s.replace('-','mul')
s=s.replace('/','add')
s=s.replace('+','div')
s=s.replace('*','sub')
s=s.replace('mul','*')
s=s.replace('add','+')
s=s.replace('div','/')
s=s.replace('sub','-')
try:
    ans=float(eval(s))
    print('%.2f'%ans)
except:   
    print('Cannot be divided by 0')
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/101997bcba6147188fa1e36a35b7542b），发布于 2020-06-05。
