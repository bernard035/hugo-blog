---
title: "python中的堆/优先队列"
date: 2021-01-07T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
python中的优先队列的写法，记录一下。

```
n,k=map(int,input().split())
l=list(map(int,input().split()))
import heapq
heapq.heapify(l)
while len(l) > 1 :
    a=heapq.heappop(l)
    b=heapq.heappop(l)
    heapq.heappush(l, a*b+k)
print(l[0]%1000000007)
```

另外读入也可以这样写，学到了

```
n,k,*l=map(int,open(0).read().split())
```

上面的代码不到500ms，下面的代码接近1500ms

```
from queue import PriorityQueue
n, k = map(int, input().split())
l = list(map(int, input().split()))
q = PriorityQueue()
for i in l:
    q.put(i)
for _ in range(n-1):
    a = q.get()
    b = q.get()
    q.put(a*b+k)
print(q.get() % 1000000007)
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/831c471ee9864e2385e83ca05053c9bc），发布于 2021-01-07。
