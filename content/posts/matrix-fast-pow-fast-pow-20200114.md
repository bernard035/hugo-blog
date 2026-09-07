---
title: "从快速幂运算到矩阵快速幂"
date: 2020-01-14T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/算法笔记"]
tags: ["模板"]
---
# 快速幂运算

HDU2035 求$a^bmod1000$  
<http://acm.hdu.edu.cn/showproblem.php?pid=2035>  
题目是很简单的，因为b也不大所以时间复杂度为n的算法也能ac

```
#include <iostream>
using namespace std;
int a, b;
int pow(int a,int b,int mod){
    int ans=1;
    for(int i=0;i<b;i++) ans=ans*a%mod;
    return ans;
}
int main(){
    while(cin>>a>>b){
            if(!a&&!b)return 0;
        cout<<pow(a,b,1000)<<endl;
    }
    return 0;
}
```

但是如果b大一点的话，可能就会TLE了。  
时间复杂度log(n)的算法：快速幂运算

```
#include <iostream>
using namespace std;
int a, b;
int qkpow(int a, int b, int mod){
    int ans = 1;
    while (b){
        if(b&1) ans =ans*a%mod; //如果b是奇数，手动乘一次
        a=a*a%mod; //a=a的平方
        b>>=1; //此处等效于b/=2
    }//也就是说 循环的出口就是当b=0 上一次b=1的时候
    return ans;
}//这样做的时间复杂度是log(n)
//本质是把指数拆分为二进制数之和
int main(){
    while(cin>>a>>b){
            if(!a&&!b)return 0;
        cout<<qkpow(a,b,1000)<<endl;
    }
    return 0;
}
```

大概这也是求逆元的基础吧。

# 矩阵快速幂

此处建议自行了解一点线性代数的相关知识：矩阵乘法。  
简单来说，矩阵快速幂就是在快速幂的基础上，其乘法用矩阵乘法来代替。  
因为是一个矩阵乘自己，根据矩阵乘法的定义，该矩阵只能是方阵。  
矩阵提供了一个手段实现用自身乘自身来代替**递推**，而且是快速递推，时间复杂度是log(n)。  
理论上说，递推均可用矩阵快速幂。

```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e2+5;
int a[N][N],b[N][N],c[N][N];
void input(int p[][N],int x){
    for(int i=0;i<x;i++)
    for(int j=0;j<x;j++)
    scanf("%d",&p[i][j]);
}
void output(int p[][N],int x){
    for(int i=0;i<x;i++){
    for(int j=0;j<x;j++)
    printf("%d ",p[i][j]);
    printf("\n");
    }
}
void multi(int p1[][N],int p2[][N],int x){
    memset(p,0,sizeof(p));
    for(int i=0;i<x;i++)
        for(int j=0;j<x;j++)
            for(int k=0;k<x;k++)
                p[i][j]+=p1[i][k]*p2[k][j];
    for(int i=0; i<n; i++)
        for(int j=0; j<n; j++)
            p1[i][j]=p[i][j];
}
int res[N][N];
void Pow(int p[][N],int n,int x) {
    memset(res,0,sizeof(res));
    for(int i=0; i<x; i++) res[i][i]=1;
    while(n) {
        if(n&1)
            multi(res,p,N);//res=res*a;复制直接在multi里面实现了；
        multi(p,p,N);//a=a*a
        n>>=1;
    }
}
int main(){

}
```

## 斐波那契数列

<http://poj.org/problem?id=3070>  
题目：斐波那契数列f(n),给一个n，求f(n)%10000,n<=1e9;

其实我感觉我这种写法比那种结构体模板的干净啊。  
斐波那契数列的递推式：  
$$f(0)=0,f(1)=1$$  
$$f(n)=f(n-1)+f(n-2)$$  
斐波那契数列的矩阵递推式：  
$$\left[ \begin{matrix} 1 & 1 \\ 1 & 0 \\ \end{matrix} \right]*\left[ \begin{matrix} f(n-1) \\ f(n-2) \\ \end{matrix} \right]=\left[ \begin{matrix} f(n)\\ f(n-1) \\ \end{matrix} \right]$$  
注意递推式的缺省，此处感谢青竹大佬  
3070也是一遍过了，代码如下：

```
#include<iostream>
#include<cstring>
using namespace std;
typedef long long ll;
const int N=2;
const int mod=10000;
void multi(ll p1[][N],ll p2[][N],int x) {
    ll p[N][N];
    memset(p,0,sizeof(p));
    for(int i=0; i<x; i++)
        for(int j=0; j<x; j++)
            for(int k=0; k<x; k++)
                p[i][j]+=p1[i][k]*p2[k][j]%mod;
    for(int i=0; i<x; i++)
        for(int j=0; j<x; j++)
            p1[i][j]=p[i][j];
}

ll res[N][N];
void Pow(ll p[][N],ll n) {
    memset(res,0,sizeof(res));
    for(int i=0; i<N; i++) res[i][i]=1;
    while(n) {
        if(n&1) multi(res,p,N);
        multi(p,p,N);
        n>>=1;
    }
}

int main() {
    ll n;
    while(cin>>n) {
        if(n==-1) break;
        ll a[N][N]= {1,1,1,0};
        Pow(a,n-1);
        cout<<res[0][0]%mod<<endl;
    }
    return 0;
}
```

总而言之，利用矩阵快速幂实现快速递推的关键就是找到递推式，**构建初始矩阵**

## 常用矩阵递推式

搬运自<https://blog.csdn.net/zhangxiaoduoduo/article/details/81807338>

> $f(n)=a*f(n-1)+b*f(n-2)+c；$
>
> $\left[ \begin{matrix} a & b & 1\\ 1 & 0 & 0\\ 0 & 0 & 1\\ \end{matrix} \right]*\left[ \begin{matrix} f(n-1)\\ f(n-2) \\ C \\ \end{matrix} \right]=\left[ \begin{matrix} f(n)\\ f(n-1) \\ C \\ \end{matrix} \right]$

> $f(n)=c^n-f(n-1)$
>
> $\left[ \begin{matrix} -1 & C \\ 0 & C \\ \end{matrix} \right]*\left[ \begin{matrix} f(n-1) \\ C^{n-1} \\ \end{matrix} \right]=\left[ \begin{matrix} f(n) \\ c^{n} \\ \end{matrix} \right]$

## 其他例题

<http://poj.org/problem?id=3233>  
<http://acm.hdu.edu.cn/showproblem.php?pid=2276>

# 数三角形

## 题目

**Problem Description**  
小朋友们都知道将一个三角形三条边的中点连线会划分成四个小的三角形，如果一直这样划分下去将会出现无数个三角形，如下所示：  
![图片说明](/images/8baf15f343089098.png "图片标题")  
小朋友发现里面有两种三角形，小朋友将 △ 命名为“帅气”的三角形 ，将 ▽ 命名为“漂亮”的三角形，小朋友想知道经过 N 次划分之后有多少个“帅气”的三角形，初始的时候只有一个“帅气”的三角形，比如上面示例中划分一次之后就有3个“帅气”的三角形了，划分两次就有10个“帅气”的三角形了。  
**Input**  
多组输入，一行包含一个整数 N（0≤N≤10e18） ,表示划分的次数。  
**Output**  
输出一个整数表示划分 N次后 “帅气”的三角形的数量，结果对 1000000007（10e9 +7）求余数。  
**Sample Input**  
0  
9  
999  
**Sample Output**  
1  
131328  
265758117

## solution

通过观察我们可以发现：  
每个上三角完成一次划分后，生成3个上三角和1个下三角；  
每个下三角完成一次划分后，生成1个下三角和3个上三角。  
即，设第i次划分后上三角个数为$x_i$，下三角个数为$y_i$，有

$$x_{i}=3x_{i-1}+y_{i-1}$$  
$$y_{i}=x_{i-1}+3y_{i-1}$$

由此我们可以完成递推。

```
#include<iostream>
#include<cstdio>
using namespace std;
const int N=1e3+7;
const int mod=1e9+7;
int main() {
    long long x[N],y[N];
    x[0]=1,y[0]=0;
    for(int i=1;i<=1000;++i){
        x[i]=(3*x[i-1]+y[i-1])%mod;
        y[i]=(x[i-1]+3*y[i-1])%mod;
    }
    int n;
    while(cin>>n) cout<<x[n]<<endl;
    return 0;
}
```

但是，这样的做法的时间复杂度是O(n)，这也就意味着在（0≤N≤10e18）的前提下一定会超时。  
那么O(logn)的做法是什么呢？  
矩阵快速幂。  
要使用矩阵快速幂，我们就要推导出来转移矩阵。  
$$\left[ \begin{matrix} x_{i-1} & y_{i-1} \\ 0 & 0 \\ \end{matrix} \right] *\left[ \begin{matrix} a & b \\ c & d \\ \end{matrix} \right]=\left[ \begin{matrix} ax_{i-1}+cy_{i-1} & bx_{i-1}+dy_{i-1} \\ 0 & 0 \\ \end{matrix} \right]=\left[ \begin{matrix} 3x_{i-1}+y_{i-1} & x_{i-1}+3y_{i-1} \\ 0 & 0 \\ \end{matrix} \right]$$  
解得 转移矩阵为$\left[ \begin{matrix} 3 & 1 \\ 1 & 3 \\ \end{matrix} \right]$

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll mod = 1e9 + 7;
struct node {  //矩阵类
    ll matrix[2][2];
    node() { memset(matrix, 0, sizeof(matrix)); }  //初始化
    void one() {                                   //单位矩阵E
        matrix[0][0] = 1;
        matrix[1][1] = 1;
    }
    node operator*(node other) {  //对*重载 定义矩阵乘法
        node ans;                 //记录乘积
        for (int i = 0; i < 2; i++)
            for (int j = 0; j < 2; j++)
                for (int k = 0; k < 2; k++) {
                    ans.matrix[i][j] += matrix[i][k] * other.matrix[k][j];
                    ans.matrix[i][j] %= mod;
                }
        return ans;
    }
};
node power(node a, ll b) { //快速幂 矩阵a的b次方
    node res;
    res.one();  //单位矩阵
    while (b) {
        if (b & 1) res = res * a;
        a = a * a;
        b >>= 1;
    }
    return res;
}
int main() {
    node temp;
    temp.matrix[0][0] = 3;
    temp.matrix[0][1] = 1;
    temp.matrix[1][0] = 1;
    temp.matrix[1][1] = 3;
    ll n, flag = 0;
    while (cin >> n) {
        if (n == 0)
            cout << 1 << endl;
        else
            cout << power(temp, n).matrix[0][0] << endl;
    }
    return 0;
}
```

这个代码是杨文冠的，我稍微改了一点。  
然后格式我也改了一下，这个格式是正常的格式（PE警告  
我在比赛的时候卡住了，没有想出来转移矩阵怎么解……  
我没想出来转移矩阵怎么解，又要求O(logn)的话怎么办呢？  
快速幂。  
因为

$$x_{i}=3x_{i-1}+y_{i-1}$$  
$$y_{i}=x_{i-1}+3y_{i-1}$$  
所以

$$x_{i}+y_{i}=4x_{i-1}+4y_{i-1}=4(x_{i-1}+y_{i-1})$$  
$$x_{i}-y_{i}=2x_{i-1}-2y_{i-1}=2(x_{i-1}-y_{i-1})$$  
所以  
$$x_{n}=(4^{n}+2^{n})/2$$

最后注意模1e9+7不能用除法，/2等效于\*它的逆元。这里涉及到费马小定理，不展开，有兴趣自己去了解。

```
#include<iostream>
#include<cstdio>
using namespace std;
typedef long long ll;
ll mod=1e9+7;
ll inv=500000004;
ll qkpow(ll a,ll b) {
    ll ans=1;
    while(b) {
        if(b&1) ans=ans*a%mod;
        a=a*a%mod;
        b>>=1;
    }
    return ans;
}
int main() {
    ll n;
    while(cin>>n) cout<<(qkpow(4,n)+qkpow(2,n))*inv%mod<<endl;
    return 0;
}
```

# 模板

## qkpow & inv

```
ll qkpow(ll a,ll b) {
    ll ans=1;
    while(b) {
        if(b&1) ans=ans*a%mod;
        a=a*a%mod;
        b>>=1;
    }
    return ans;
}
ll getInv(ll a){return qkpow(a,mod-2);} //求一个数的逆元
```

## 矩阵快速幂

杨文冠的2\*2

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll mod = 1e9 + 7;
struct node {  //矩阵类
    ll matrix[2][2];
    node() { memset(matrix, 0, sizeof(matrix)); }  //初始化
    void one() {                                   //单位矩阵E
        matrix[0][0] = 1;
        matrix[1][1] = 1;
    }
    node operator*(node other) {  //对*重载 定义矩阵乘法
        node ans;                 //记录乘积
        for (int i = 0; i < 2; i++)
            for (int j = 0; j < 2; j++)
                for (int k = 0; k < 2; k++) {
                    ans.matrix[i][j] += matrix[i][k] * other.matrix[k][j];
                    ans.matrix[i][j] %= mod;
                }
        return ans;
    }
};
node power(node a, ll b) { //快速幂 矩阵a的b次方
    node res;
    res.one();  //单位矩阵
    while (b) {
        if (b & 1) res = res * a;
        a = a * a;
        b >>= 1;
    }
    return res;
}
int main() {
    node temp;
    temp.matrix[0][0] = 3;
    temp.matrix[0][1] = 1;
    temp.matrix[1][0] = 1;
    temp.matrix[1][1] = 3;
    ll n, flag = 0;
    while (cin >> n) {
        if (n == 0)
            cout << 1 << endl;
        else
            cout << power(temp, n).matrix[0][0] << endl;
    }
    return 0;
}
```

我自己写的

```
#include<iostream>
#include<cstring>
using namespace std;
typedef long long ll;
const int N=2;
const int mod=10000;
void multi(ll p1[][N],ll p2[][N],int x) {
    ll p[N][N];
    memset(p,0,sizeof(p));
    for(int i=0; i<x; i++)
        for(int j=0; j<x; j++)
            for(int k=0; k<x; k++)
                p[i][j]+=p1[i][k]*p2[k][j]%mod;
    for(int i=0; i<x; i++)
        for(int j=0; j<x; j++)
            p1[i][j]=p[i][j];
}

ll res[N][N];
void Pow(ll p[][N],ll n) {
    memset(res,0,sizeof(res));
    for(int i=0; i<N; i++) res[i][i]=1;
    while(n) {
        if(n&1) multi(res,p,N);
        multi(p,p,N);
        n>>=1;
    }
}

int main() {
    ll n;
    while(cin>>n) {
        if(n==-1) break;
        ll a[N][N]= {1,1,1,0};
        Pow(a,n-1);
        cout<<res[0][0]%mod<<endl;
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/68d02876662a42b98ab8923920f8434c），发布于 2020-01-14。
