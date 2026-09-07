---
title: "三棱锥之刻 简单计算几何"
date: 2021-02-01T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "计算几何"]
---
![图片说明](/images/ad5c1233a3d872cb.png)

根据发射距离R的不同，可分为四种情况

1. 碰不到
2. 四个圆
3. 圆和三角的交\*4
4. 正四面体的全部内表面

因为第三种情况忘记\*4浪费了一个晚上。

![图片说明](/images/7a6865ce9b0b21e7.png)

具体来说就是计算小三角形的面积，再加上一个最小的扇形面积。

重合部分的面积等于${S_{\triangle DGK} *3 +\frac{1}{2}S_{扇HDG}*r*6}$

```
from math import sqrt, acos, pi
a, R = map(float, input().split())
d = sqrt(6) * a / 12  #中心到面的距离
r = sqrt(R * R - d * d) if d < R else 0  #三角形上的圆的半径
if R <= d: ans = 0  #碰不到
elif r <= a / (sqrt(3) * 2):  #四个圆
    ans = 4 * pi * (R * R - d * d)
elif R >= a * sqrt(6) / 4:  #全覆盖
    ans = a * a * sqrt(3)
else:  #切掉
    DF = a / (2 * sqrt(3))
    GF = sqrt(r * r - DF * DF)  #求底面三角形 半底长
    deg = pi / 3 - acos(DF / r)  # 小扇形的弧度
    ans = GF * DF * 3
    shan = deg * 0.5 * r * r
    ans += 6 * shan
    ans *= 4  #就漏了这一句
print(ans)
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/9ce2332c8a0a4f9894c062636433b4fb），发布于 2021-02-01。
