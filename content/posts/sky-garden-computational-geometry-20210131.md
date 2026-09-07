---
title: "Sky Garden 计算几何"
date: 2021-01-31T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解", "计算几何"]
---
## 题意

有$n$个同心圆，半径分别是$1\sim n$，有$m$条直线将这些同心圆切分成$2m$等分。

求所有交点两两之间的最短距离的和。

![图片说明](/images/fc1e753d63b71669.png "图片标题")

## 思路

1. 圆心到每个点之间的距离是很好算的$\sum _{r=1}^{n}2mr$
2. 单个环内部每个点之间的距离和也是很好算的：枚举两点之间的长度，所有的相邻的点的最短距离和会贡献$2\pi r$，所有的间隔一个点的最短距离和也是$2\pi r$，直到两点恰好在直径两端的时候，贡献$2r$，共计$2\pi r*(m-1)+2mr$，把每个圆的贡献累加，就是$\sum _{r=1}^{n}2\pi r*(m-1)+2mr$
3. 考虑外层到内层的点的距离和，首先可以肯定，外层到内层的对应点可以直接走直线，距离$r_2-r_1$，于是就继承了这个点与该内层所有点的最短距离，累加外圈所有点，每多一圈，内层就要多算一遍，而所有的直线也被类加成了立体的锥形。

## Solution

```
#include <bits/stdc++.h>
using namespace std;
typedef double db;
const int N = 510;
const db pi = acos(-1);
db point[N];  // 表示本层的某个点 到本层所有其他点的距离和
db cir[N];  // 表示当前层的某个点 到本层和内层所有点的距离和
int main() {
    db n, m, ans = 0;
    cin >> n >> m;
    ans += (m > 1) * n * (n + 1) * m;  // 直接把圆心到每个点的距离算出来
    // 等差序列 和我上面说的“锥形”一样
    db th = pi / (1.0 * m);          // 表示相邻两个点构成的角度
    for (int i = 1; i <= n; i++) {   // 由内而外
        for (int j = 1; j < m; j++)  // 对于一个点，枚举一半的点对
            point[i] += min(2.0 * i, th * j * i);  // 走圆弧 或者 走半径
        point[i] *= 2;                             // 另外一半的点对
        point[i] += 2 * i;                         //直径
        ans += point[i] * m;                       //本层圆上的
        ans += (cir[i - 1] + (i - 1) * 2.0 * m) * 2.0 * m;  //本层圆通往内部的
        // 内层一共有（i-1)*2*m个点  外层有2*m个点
        // 对于每个外层圆上的点 要先降到内层的对应点上
        // 每个从外层降到内层的pair 都要加上一个额外的 降到内一层的消耗
        cir[i] = cir[i - 1] + (i - 1) * 2.0 * m + point[i];
        // 外层+内层 此处做前缀和处理，可避免上面的等差数列求和
        //ans+=cir[i]
    }
    printf("%.10lf\n", ans);
    // printf("%.10lf\n", cir[(int)n] * 2 * m + ans);
    return 0;
}
```

```
//逆十字 O(n^3)
#include<bits/stdc++.h>
using namespace std;
double pi=acos(-1);
int n,m;
int main(){
    scanf("%d%d",&n,&m);
    double ans=0;
    for (int i=1;i<=n;i++)
        for (int j=i;j<=n;j++)
            for (int k=0;k<=m;k++)
                ans+=min(i+j*1.0,i*k*pi/m+j-i)*(k==0||k==m?1:2)*(j==i?1:2);
    ans*=2*m;
    if (m!=1){
        for (int i=1;i<=n;i++) ans+=4*m*i;
    }
    printf("%.15lf\n",ans/2);
}
```

```
//O(1)
#include <bits/stdc++.h>
using namespace std;
const double pi = acos(-1);
int main() {
    long double n, m, s = 0, k1, k2, t;
    cin >> n >> m;
    k1 = (1 + n) * n * n / 2 - n * (n + 1) * (2 * n + 1) / 6;
    k2 = (1 + n) * n * (0.5 + n) / 2 - n * (n + 1) * (2 * n + 1) / 6;
    s += (1 + n) * n * m;
    if (m == 1) s = 0;
    s += 4 * m * m * k1;
    t = floor(2 * m / pi);
    s += (2 * pi * t * (t + 1) + (4 * m * (m - t) - 2 * m) * 2) * k2;
    cout << fixed << setprecision(10) << s;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/d92e342be3b144eb950fa82cfa1ae630），发布于 2021-01-31。
