---
title: "寒假训练赛5 三分 贪心 博弈"
date: 2020-02-14T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/赛后分析"]
tags: ["赛后分析", "牛客", "贪心", "博弈"]
---
# 总结

是收获颇丰的一场，明天醒来补题，现在记下来，免得忘记了。

1. 圆周率的表示方法`const double PI=acos(-1);`，我之前记过，但是忘记了，这次还是写的 `const double PI=3.14159264354;`(这个也是我背下来的 【J题】<https://ac.nowcoder.com/acm/contest/view-submission?submissionId=42948838&returnHomeType=1&uid=538133338>- sin函数的使用方法 `2*r*sin(PI/n)` 表示圆内接多边形的边长，因为sin里面是弧度制，所以要特别注意- 二进制位数 log函数 十进制位数- 取余的基本原理 加减乘不会影响到取余结果 也就是说分步取余和一起取余的结果是一样的

# B题

## 题意

有n个点，在x轴上找一个点，让这个点到n个点中最远的点距离最短。  
也就是

> 求选择的比赛地距离各训练基地最大距离的最小值

## 思路

出题人：

> 题目要求最大距离最小，很容易想到使用二分或者三分的方法去做。  
> 这题使用三分会比较简单，二分会比较麻烦。

---

其实我是想过用二分来写的，但是可能是写出来就变味了。另外一边写就一边感觉不对劲：结果不是随着x轴右移增加或减少的，它不一定是线性变化的。  
中间我还提出了“拟合圆”，但不知道怎么表达。

---

我看了青竹大佬的二分写法。

1. 不是对x的坐标二分，而是对结果的值二分；- check函数的写法很关键：对圆心的取值范围判断是否有可能。  
     ![图片说明](/images/9ed2a2e6973deee7.png "图片标题")

## 代码

### 青竹大佬 二分之王

```
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1e5 + 7;
const double eps = 1e-6;
typedef long long ll;
int n;
struct node {
    int x, y;
} p[maxn];
bool check(double len) {
    double left = -1000000000, right = 1000000000;
    for (int i = 1; i <= n; i++) {
        if(fabs(p[i].y*1.0) > len) return false;
        //如果有某个点纵坐标绝对值大于len，len肯定就太短了  
        double tmp = sqrt(len*len*1.0 - 1.0*p[i].y*p[i].y);
        //勾股定理 把每个点都当成最远点 得到圆心区间 
        double l = p[i].x - tmp, r = p[i].x + tmp;
        left = max(left, l);
        right = min(right, r);
    }
    return left <= right;
}
int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++)
        scanf("%d%d", &p[i].x, &p[i].y);
    double left = 0, right = 10000000000, ans = 0;
    while (right - left > eps) {
        double mid = left+(right-left)/2;
        if(check(mid)) ans = mid,right = mid;
        else left = mid;
    }
    printf("%.6f\n", ans);
    return 0;
}
```

这个浮点数的二分写法也很值得借鉴，当作一个模板。

### 出题人的三分

作者：儒生雄才1  
链接：<https://ac.nowcoder.com/discuss/366644?type=101&order=0&pos=5&page=2>

```
#include <bits/stdc++.h>
using namespace std;

struct p{
    int x,y;
}a[100005];
int n;
double check(double x){
    double max=0;
    for (int i=1;i<=n;i++){
        double tmp=sqrt(a[i].y*a[i].y+(a[i].x-x)*(a[i].x-x));
        if (tmp>max) max=tmp;
    }
    return max;
}
double tsearch(double left,double right){
    int i;
    double mid,midmid;
    for(i=0;i<100;i++){
        mid=left+(right-left)/2;
        midmid=mid+(right-mid)/2;
        if(check(mid)>check(midmid)) //极大值求法
            left=mid;
        else
            right=midmid;
    }
    return mid;
}
int main(){
    scanf("%d",&n);
    for (int i=1;i<=n;i++)
        scanf("%d%d",&a[i].x,&a[i].y);
    double max=tsearch(-10000,10000);
    printf("%.4lf\n",check(max));
    return 0;
}
```

我大概理解了二分搜索，二分搜索（和它衍生的x分搜索）的本质是通过高效缩小范围，最终锁定到一个最佳值。

---

**三分搜索！**

> 当要求一个凸函数或者凹函数的极值时，可以使用三分搜索。

我明白了。

---

有一类函数被称为**单峰函数**，它们拥有**唯一**的极大值点，在极大值点左侧**严格**单调上升，右侧**严格**单调下降；或者拥有**唯一**的极小值点，在极小值点左侧严格单调下降，在极小值点右侧严格单调上升（**单谷函数**）。对于单峰函数和单谷函数，我们可以通过**三分法**求其**极值**。  
以单峰函数f为例，我们在函数定义域[l,r]上任取两个点lmid与rmid，把函数分成三段。

1. 若f(lmid)<f(rmid)，则lmid与rmid要么同时处于极大值点左侧（单调上升函数段），要么处于极大值点两侧。无论哪种情况下，极大值点都在lmid右侧，可令l=lmid。- 同理，若f(lmid)>f(rmid)则极大值点一定在rmid左侧，可令r=rmid。

如果我们取lmid与rmid为三等分点，那么定义域范围每次缩小1/3。如果我们取lmid与rmid在二等分点两侧极其接近的地方，那么定义域范围每次近似缩小1/2.通过log级别的时间复杂度即可在指定精度下求出极值，这就是三分法。  
注意：我们在介绍单峰函数时特别强调了“严格”单调性。若三分过程中遇到f(lmid)=f(rmid)，当函数严格单调时，令l=lmid或r=rmid均可。如果函数不严格单调，即在函数种存在一段值相等的部分，那么我们无法判断定义域左右边界如何缩小，三分法就不再适用。

感谢YLM大佬的帮助。

### 三分模板题

HDU2899 acm.hdu.edu.cn/showproblem.php?pid=2899

```
#include <iostream>
#include <algorithm>
#include <cmath>
#include <cstdio>
using namespace std;
const double eps=1e-8;
double y;
double F(double x) {
    return 6*(pow(x,7.0))+8*(pow(x,6.0))+7*(pow(x,3.0))+5*(pow(x,2.0))-y*x;
}
int main() {
    int T;
    cin>>T;
    while(T--) {
        scanf("%lf",&y);
        double r,l,R=100.0,L=0.0;
        while(fabs(R-L)>eps) {
            l=L+(R-L)/2;
            r=l+(R-l)/2;
            if(F(l)-F(r)<eps) R=r;
            else L=l;
        }
        printf("%.4lf\n",F(l));
    }
    return 0;
}
```

# D题

## 题意

从a到b，1单位时间可以移动1单位距离，也可以闪现到$\sqrt[3]{a}$ 上，求最短时间。

## 思路

出题人说是贪心，只要闪现移动距离大于走路就闪现。我当时也是这样想的，然后疯狂吃wa。

## 代码

我的wa代码

```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+5;
const double eps=1e-6;
int main() {
    int T;
    cin>>T;
    while(T--) {
        double a,b;
        cin>>a>>b;
        double cost=0,sq;
        while(fabs(a-b)>eps) {
            if(a<0) {
                a=0-a;
                sq=pow(a,(1.0/3));
                sq=0-sq;
                a=0-a;
            } else sq=pow(a,(1.0/3));
            if((fabs(a-sq)>1)&&(fabs(sq-b)<fabs(a-b))) a=sq,cost+=1;
            else {
                cost+=fabs(b-a);
                break;
            }
        }
        printf("%.9f\n",cost);
    }
    return 0;
}
```

出题人的代码

```
#include<bits/stdc++.h>
using namespace std;
const double eps = 1e-8;
int main() {
    int T;
    cin>>T;
    while(T--) {
        int a, b;
        scanf("%d%d", &a, &b);
        double ans = 0;
        double ca = a, cb = b;
        double p = 1.0/3.0;
        while(1) {
            double na;
            if(ca < 0) na = -pow(-ca,p);
            else na = pow(ca, p);
            if(abs(na-cb)+1.0 < abs(ca-cb)) ans += 1.0, ca = na;
            else {
                ans += abs(ca-cb);
                break;
            }
        }
        printf("%.9f\n", ans);
    }
    return 0;
}
```

这里我仔细看了一下，我和出题人的代码只有一个地方不一样（别的地方都是他写错了或者不规范）：  
判断是否要进行位移时，我的判断条件是$\left| a-\sqrt[3]{a} \right| >1.0$ 并且$\left| \sqrt[3]{a}-b \right| <\left| a-b \right|$  
而出题人的判断条件是$\left| \sqrt[3]{a}-b \right| +1.0 <\left| a-b \right|$  
都是满足就位移，我不明白为什么我的不行

---

2020.10回来补题，发现它为什么行了。  
比如$a$和$cbrt(a)$都很接近终点了，但是不值得花费1的情况，我的代码依然会花费1.

### 我的最终代码

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 1e5 + 5;
const double eps = 1e-8;
int main() {
    int T;
    cin >> T;
    while (T--) {
        double a, b;
        cin >> a >> b;
        double cost = 0, c;
        while (fabs(a - b) > eps) {
            c = cbrt(a);
            // if ((fabs(a - c) > 1.0) && (fabs(a - b) - fabs(c - b) > eps))
            if (fabs(b - c) + 1.0 < fabs(a - b))
                a = c, cost += 1.0;
            else {
                cost += fabs(b - a);
                break;
            }
        }
        printf("%.9f\n", cost);
    }
    return 0;
}
```

### 神奇的代码

```
#include<bits/stdc++.h>
using namespace std;
int main(){
    int t;
    cin>>t;
    while(t--){
        double x,y;
        cin>>x>>y;
        double ans=fabs(x-y);
        for(int i=1;i<=16;i++){
            x=cbrt(x);
            ans=min(ans,fabs(x-y)+i);
        }
        printf("%.7lf\n",ans);
    }
    return 0;
}
```

```
#include<iostream>
#include<cmath>
using namespace std;
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        double a,b,ans = 0;
        scanf("%lf%lf",&a,&b);
        while(fabs(cbrt(a) - b) + 1 < fabs(a - b)){
            ans += 1;
            a = cbrt(a);//立方根
        }
        ans += fabs(a - b);
        printf("%.9lf\n",ans);
    }
    return 0;
}
```

# 其他题

## E题

> 找规律可以发现，当张数是2^n的时候输出Alice，否则输出Bob。  
> 如果不是2^n，先手直接拿等同于其二进制最低位的数量的张数，后手将无力还击~

### 我的位运算代码

```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+5;
ll n;
bool chk(){
    ll tmp=n;
    int w[N];
    int i;
    for(i=0;tmp;tmp>>=1)//转成二进制
        w[i++]=tmp%2;
    i--;//最高位不读
    for(int j=0;j<i;j++)
        if(w[j]) return 1;
    return 0;
}
int main(){
    cin >>n;
    if(chk()) cout<<"Bob"<<endl;
    else cout<<"Alice"<<endl;
}
```

## H题

> 观察给的哈希函数可以轻松看出，这个哈希函数就仅仅是把一个只由小写字符组成的字符串当成了一个26进制的数，不取模的情况下，任意小写字符串和自然数是一一对应的。因此，只要把给的字符串转换成对应的26进制整数，加上模数后转换回字符串，一定可以得到一个最小字典序大于原串的字符串。只要这个新字符串长度为6即是满足要求的字符串。

1. 就是逐次取模和最后取模的结果一样的，这也是取余的原理；- 我的做法是把密码换成十进制，加上mod，再换回字符串，这样做的好处是直接加，不然要写一个26进制规则运算。

### 我的代码

```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+5;
int mod;
ll H(char str[]) {
    ll res = 0;
    for (int i = 0; i < 6; i++) 
        res = (res * 26 + str[i] - 'a');
    return res;
}
int main() {
    char s[6];
    while(cin>>s>>mod) {
        ll sv=H(s);
        sv+=mod;
        if(sv>=(26*26*26*26*26*26)) cout<<"-1"<<endl;
        else {
        char a[6];
        for(int i=5;i>=0;i--){
            a[i]=sv%26+'a';
            sv/=26;
        }
        cout<<a<<endl;
        }
    }
    return 0;
}
```

### Python 代码

```
try:
    while True:
        s,mod = map(str,input().split())
        Sum = 0
        for i in s:
            Sum = (Sum*26+ord(i)-97)
        Sum += int(mod)
        if Sum >= 308915776:
            print(-1)
            continue
        ans=''
        for i in range(6):
            ans += chr(Sum%26+97)
            Sum //= 26
        print(ans[::-1])
except:
    pass
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/aa10060b4bdf45ccbb91a21501473e10），发布于 2020-02-14。
