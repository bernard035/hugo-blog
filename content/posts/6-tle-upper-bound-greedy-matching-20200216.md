---
title: "寒假训练赛6 贪心匹配 循环继承TLE upper_bound"
date: 2020-02-16T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["赛后分析", "牛客", "贪心"]
---
这次比赛我不应该贪B题的，看到钟涛做出来了我就觉得我应该也可以（但是我没搜洛谷，如果主攻D题可能就做出来了。

---

# D题

<https://ac.nowcoder.com/acm/contest/3007/D>

## 思路

其实我的思路是对的，就是对每个Bi，找有多少个比相应位置Ai后面的Ai可以换到这个位置来。  
然后有多少个，在这个位置上，就有多少种方案，就能乘多少次。  
真正麻烦的是不要做重复做的事情，内层循环继承j的值容易超时。  
最后**long long**。

## 实现

### TLE

```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+7;
const int mod=1e9+7;
const int inv=500000004;
int a[N],b[N];
ll n,k,c[N];
int main() {
    cin>>n;
    for(int i=1; i<=n; i++) cin>>a[i];
    for(int i=1; i<=n; i++) cin>>b[i];
    sort(a+1,a+1+n);
    sort(b+1,b+1+n);
    ll ans=1;
    for(int i=1; i<=n; i++) if(b[i]<a[i]) ans=0;
    if(ans) {
        ll m=1,cnt;
        for(int j=1; j<n; j++) {
            cnt=1;
            for(int i=j+1; i<=n; i++) {
                if(b[j]>=a[i]) cnt++;
                else break;
            }
            m=m*cnt%mod;
        }
        ans=m%mod;
    }
    cout<<ans<<endl;
    return 0;
}
```

### AC

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int mod=1e9+7;
int n,a[100005],b[100005];
int main(){
    cin >> n;
    for(int i=1;i<=n;i++)scanf("%d",&a[i]);
    for(int i=1;i<=n;i++)scanf("%d",&b[i]);
    sort(a+1,a+n+1); sort(b+1,b+n+1);
    ll ans=1;int pos=1;
    for(int i=1;i<=n;i++){
        while(pos<=n&&a[pos]<=b[i]) pos++;
        ans=ans*(max(0,pos-i))%mod;
    }
    cout<<ans<<endl;
    return 0;
}
```

### STL

```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+7;
const int mod=1e9+7;
const int inv=500000004;
int a[N],b[N];
ll n,k,c[N];
int main() {
    cin>>n;
    for(int i=1; i<=n; i++) cin>>a[i];
    for(int i=1; i<=n; i++) cin>>b[i];
    sort(a+1,a+1+n);
    sort(b+1,b+1+n);
    ll ans=1;
    for(int i=1; i<=n; i++) if(b[i]<a[i]) ans=0;
    if(ans) {
        ll m=1,cnt;
        for(int i=1; i<n; i++) {
            int *p=upper_bound(a+1,a+n+1,b[i]);
            int d=p-(a+i);
            ans=ans*d%mod; 
        }
    }
    cout<<ans<<endl;
    return 0;
}
```

其实这也是我第一次用upper\_bound

### Python

学习一下python有序数组的输入方式

```
n=int(input())
a=[0]+list(sorted(map(int,input().split())))
b=[0]+list(sorted(map(int,input().split())))
ans=1;pos=0
for i in range(1,n+1):
    while pos<n and a[pos+1]<=b[i]:pos+=1
    ans=ans*max(0,pos-i+1)%(10**9+7)
print(ans)
```

# F题

题目数据太水了。估计是出题人被整烦了。  
绝对不要再用来源不明的垃圾函数，本来我第一次的算法就是对的，而且比模拟更优。  
2000\*2000模拟也不可能超时（数据规模太弱

## 实现

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn=1e5+7;
const int mod=1e9+7;
const int inv=500000004;
int main() {
    ll n,m,H;
    scanf("%lld %lld %lld",&n,&m,&H);
    ll ans=0;
    while(H--) {
        ll x,y,z;
        scanf("%lld %lld %lld",&x,&y,&z);
        ll tmp=((x+1+x+m)*m/2)%mod;//行权重 
        tmp=(tmp+((y+1+y+n)*n/2)%mod)%mod;//列权重 
        tmp-=(x+y);//减去重合权重 
        ans=(ans+((tmp*z)%mod))%mod;
    }
    printf("%lld\n",ans);
    return 0;
}
```

```
#include  <bits/stdc++.h>
const int mod=1e9+7;
long long n,m,h,x,y,z,sum=0;
int main(){
    scanf("%d%d%d",&n,&m,&h);
    while(h--)
        scanf("%d%d%d",&x,&y,&z),
        sum=(sum+z*(n*(n+1)/2+m*(m+1)/2+(m-1)*x+(n-1)*y))%mod;
    printf("%lld\n",sum);
    return 0;
}
```

## 读写模板

```
inline ll read()
{
    ll s = 0, f = 1;
    char ch = getchar();
    for(; !(ch >= '0' && ch <= '9'); ch = getchar()) if(ch == '-') f = - 1;
    for(; ch >= '0' && ch <= '9'; ch = getchar()) s = ((s << 3ll) + (s << 1ll) + (ch ^ 48ll));
    return s * f ;
}
inline void out(ll x)
{
    if(x<0) putchar('-'),x=-x;
    if(x>9) out(x/10);
    putchar(x%10+'0');
}
//使用方法
 x = read();
```

# 其他

## J题签到题

任何三角形都满足，没有No的情况。  
三角形是两边之和大于第三边，反过来是<=，不要漏=  
**输出一律复制粘贴！！！**

## A题

二分可以起死回生

```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+7;
const double eps=1e-6;
int a[N],b[N];
ll n,k,c[N];
bool check(ll num){
    return c[k]>=num;
}
int main() {
    cin>>n>>k;
    for(int i=1; i<=n; i++) cin>>a[i];
    for(int i=1; i<=n; i++) cin>>b[i];
    sort(a+1,a+n+1,greater<int>());
    sort(b+1,b+n+1,greater<int>());
    for(int i=1;i<=k;i++) c[i]=a[i]+b[k-i+1];
    sort(c+1,c+k+1,greater<int>());
    ll R=a[1]+b[1],L=a[n]+b[n];
    ll ans=0;
    while(L<=R) {
        ll mid=L+((R-L)>>1);
        if(check(mid)) L=mid+1,ans=max(ans,mid);
        else R=mid-1;
    }
    cout<<ans<<endl;
    return 0;
}
```

```
#include <iostream>
#include <algorithm>
using namespace std;
const int N=1e5+7;
int a[N],b[N],c[N];
int main() {
    int n,k;
    cin>>n>>k;
    for(int i=0; i<n; i++) cin>>a[i];
    for(int i=0; i<n; i++) cin>>b[i];
    sort(a,a+n,greater<int>()); 
    sort(b,b+n,greater<int>());
    int ans=2e9+1;
    for(int i=0; i<k; i++) c[i]=a[i]+b[k-1-i],ans=min(ans,c[i]);
    cout<<ans<<endl;
    return 0;
}
```

```
n,k=map(int,input().split())
a=sorted(list(map(int, input().split())))
b=sorted(list(map(int, input().split())))
ans=1e9
for i in range(n-k,n):ans=min(ans,a[i]+b[n+n-k-i-1])
print(ans)
```

![图片说明](/images/5a4a81536db2b53c.png "图片标题")  
最后G题的stack是临时学的

# 邪教仪式

```
//https://ac.nowcoder.com/acm/contest/view-submission?submissionId=43011958
/***********************************************
 *                   _ooOoo_                   *
 *                  o8888888o                  *
 *                  88" . "88                  *
 *                  (| -_- |)                  *
 *                  O\  =  /O                  *
 *               ____/`---'\____               *
 *             .'  \\|     |//  `.             *
 *            /  \\|||  :  |||//  \            *
 *           /  _||||| -:- |||||-  \           *
 *           |   | \\\  -   * |    |           *
 *           | \_|  ''\---/''  |   |           *
 *           \  .-\__  `-`  ___/-. /           *
 *         ___`. .'  /--.--\  `. . __          *
 *      ."" '_/___.'  >'"".       *
 *     | | :  `- \`.;`\ _ /`;.`/ - ` : | |     *
 *     \  \ `-.   \_ __\ /__ _/   .-` /  /     *
 *======`-.____`-.___\_____/___.-`____.-'======*
 *                   `=---='                   *
 *^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
             佛祖保佑       永无BUG
   本程序已经经过开光处理，绝无可能再产生bug
 **********************************************/
/***                  .
                     ';;;;;.
                    '!;;;;;;!;`
                   '!;|&#@|;;;;!:
                  `;;!&####@|;;;;!:
                 .;;;!&@$$%|!;;;;;;!'.`:::::'.
                 '!;;;;;;;;!$@###&|;;|%!;!$|;;;;|&&;.
                 :!;;;;!$@&%|;;;;;;;;;|!::!!:::;!$%;!$%`    '!%&#########@$!:.
                 ;!;;!!;;;;;|$$&@##$;;;::'''''::;;;;|&|%@$|;;;;;;;;;;;;;;;;!$;
                 ;|;;;;;;;;;;;;;;;;;;!%@#####&!:::;!;;;;;;;;;;!&####@%!;;;;$%`
                `!!;;;;;;;;;;!|%%|!!;::;;|@##%|$|;;;;;;;;;;;;!|%$#####%;;;%&;
                :@###&!:;;!!||%%%%%|!;;;;;||;;;;||!$&&@@%;;;;;;;|$$##$;;;%@|
                ;|::;;;;;;;;;;;;|&&$|;;!$@&$!;;;;!;;;;;;;;;;;;;;;;!%|;;;%@%.
               `!!;;;;;;;!!!!;;;;;$@@@&&&&&@$!;!%|;;;;!||!;;;;;!|%%%!;;%@|.
            %&&$!;;;;;!;;;;;;;;;;;|$&&&&&&&&&@@%!%%;!||!;;;;;;;;;;;;;$##!
            !%;;;;;;!%!:;;;;;;;;;;!$&&&&&&&&&&@##&%|||;;;!!||!;;;;;;;$&:
            ':|@###%;:;;;;;;;;;;;;!%$&&&&&&@@$!;;;;;;;!!!;;;;;%&!;;|&%.
             !@|;;;;;;;;;;;;;;;;;;|%|$&&$%&&|;;;;;;;;;;;;!;;;;;!&@@&'
              .:%#&!;;;;;;;;;;;;;;!%|$$%%&@%;;;;;;;;;;;;;;;;;;;!&@:
              .%$;;;;;;;;;;;;;;;;;;|$$$$@&|;;;;;;;;;;;;;;;;;;;;%@%.
                !&!;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;|@#;
                 `%$!;;;;;;;;;;;$@|;;;;;;;;;;;;;;;;;;;;;;;;!%$@#@|.
                   .|@%!;;;;;;;;;!$&%||;;;;;;;;;;;;;;;;;!%$$$$$@#|.
                       ;&$!;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;%#####|.
                       |##$|!;;;;;;::'':;;;;;;;;;;;;;!%$$$@#@;
                      ;@&|;;;;;;;::'''''':;;;;;;;|$&@###@|`
                    .%##@|;;;;:::''''''''''::;!%&##$'
                  `$##@$$@@&|!!;;;:'''''::::;;;;;|&#%.
                ;&@##&$%!;;;;;;::''''''''::;!|%$@#@&@@:
                 .%@&$$|;;;;;;;;;;:'''':''''::;;;%@#@@#%.
                :@##@###@$$$$$|;;:'''':;;!!;;;;;;!$#@@#$;`
                 `%@$$|;;;;;;;;:'''''''::;;;;|%$$|!!&###&'
                 |##&%!;;;;;::''''''''''''::;;;;;;;!$@&:`!'
                :;!@$|;;;;;;;::''''''''''':;;;;;;;;!%&@$:                !@#$'
                  |##@@&%;;;;;::''''''''':;;;;;;;!%&@#@$%:            '%%!%&;
                  |&%!;;;;;;;%$!:''''''':|%!;;;;;;;;|&@%||`         '%$|!%&;
                  |@%!;;!!;;;||;:'''''':;%$!;;;;!%%%&#&%$&:        .|%;:!&%`
                  !@&%;;;;;;;||;;;:''::;;%$!;;;;;;;|&@%;!$;       `%&%!!$&:
                  '$$|;!!!!;;||;;;;;;;;;;%%;;;;;;;|@@|!$##;      !$!;:!$&:
                   |#&|;;;;;;!||;;;;;;;;!%|;;;;!$##$;;;;|%'   `%$|%%;|&$'
                    |&%!;;;;;;|%;;;;;;;;$$;;;;;;|&&|!|%&&;  .:%&$!;;;:!$@!
                    `%#&%!!;;;;||;;;;;!$&|;;;!%%%@&!;;;!!;;;|%!;;%@$!%@!
                    !&!;;;;;;;;;||;;%&!;;;;;;;;;%@&!;;!&$;;;|&%;;;%@%`
                   '%|;;;;;;;;!!|$|%&%;;;;;;;;;;|&#&|!!||!!|%$@@|'
                   .!%%&%'`|$;     :|$#%|@#&;%#%.
 *               ii.                                         ;9ABH,
 *              SA391,                                    .r9GG35&G
 *              &#ii13Gh;                               i3X31i;:,rB1
 *              iMs,:,i5895,                         .5G91:,:;:s1:8A
 *               33::::,,;5G5,                     ,58Si,,:::,sHX;iH1
 *                Sr.,:;rs13BBX35hh11511h5Shhh5S3GAXS:.,,::,,1AG3i,GG
 *                .G51S511sr;;iiiishS8G89Shsrrsh59S;.,,,,,..5A85Si,h8
 *               :SB9s:,............................,,,.,,,SASh53h,1G.
 *            .r18S;..,,,,,,,,,,,,,,,,,,,,,,,,,,,,,....,,.1H315199,rX,
 *          ;S89s,..,,,,,,,,,,,,,,,,,,,,,,,....,,.......,,,;r1ShS8,;Xi
 *        i55s:.........,,,,,,,,,,,,,,,,.,,,......,.....,,....r9&5.:X1
 *       59;.....,.     .,,,,,,,,,,,...        .............,..:1;.:&s
 *      s8,..;53S5S3s.   .,,,,,,,.,..      i15S5h1:.........,,,..,,:99
 *      93.:39s:rSGB@A;  ..,,,,.....    .SG3hhh9G&BGi..,,,,,,,,,,,,.,83
 *      G5.G8  9#@@@@@X. .,,,,,,.....  iA9,.S&B###@@Mr...,,,,,,,,..,.;Xh
 *      Gs.X8 S@@@@@@@B:..,,,,,,,,,,. rA1 ,A@@@@@@@@@H:........,,,,,,.iX:
 *     ;9\. ,8A#@@@@@@#5,.,,,,,,,,,... 9A. 8@@@@@@@@@@M;    ....,,,,,,,,S8
 *     X3    iS8XAHH8s.,,,,,,,,,,...,..58hH@@@@@@@@@Hs       ...,,,,,,,:Gs
 *    r8,        ,,,...,,,,,,,,,,.....  ,h8XABMMHX3r.          .,,,,,,,.rX:
 *   :9, .    .:,..,:;;;::,.,,,,,..          .,,.               ..,,,,,,.59
 *  .Si      ,:.i8HBMMMMMB&5,....                    .            .,,,,,.sMr
 *  SS       :: h@@@@@@@@@@#; .                     ...  .         ..,,,,iM5
 *  91  .    ;:.,1&@@@@@@MXs.                            .          .,,:,:&S
 *  hS ....  .:;,,,i3MMS1;..,..... .  .     ...                     ..,:,.99
 *  ,8; ..... .,:,..,8Ms:;,,,...                                     .,::.83
 *   s&: ....  .sS553B@@HX3s;,.    .,;13h.                            .:::&1
 *    SXr  .  ...;s3G99XA&X88Shss11155hi.                             ,;:h&,
 *     iH8:  . ..   ,;iiii;,::,,,,,.                                 .;irHA
 *      ,8X5;   .     .......                                       ,;iihS8Gi
 *         1831,                                                 .,;irrrrrs&@
 *           ;5A8r.                                            .:;iiiiirrss1H
 *             :X@H3s.......                                .,:;iii;iiiiirsrh
 *              r#h:;,...,,.. .,,:;;;;;:::,...              .:;;;;;;iiiirrss1
 *             ,M8 ..,....,.....,,::::::,,...         .     .,;;;iiiiiirss11h
 *             8B;.,,,,,,,.,.....          .           ..   .:;;;;iirrsss111h
 *            i@5,:::,,,,,,,,.... .                   . .:::;;;;;irrrss111111
 *            9Bi,:,,,,......                        ..r91;;;;;iirrsss1ss1111***/
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/904b458615fe40f09888cf1c7d60ff9e），发布于 2020-02-16。
