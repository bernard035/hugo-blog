---
title: "第十二届蓝桥杯大赛软件赛省赛 C/C++ 大学 B 组"
date: 2021-04-18T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题集", "蓝桥杯"]
---
## 试题 A: 空间

![图片说明](/images/4a79ef1e23c79fc1.png "图片标题")

1 byte=8 bit1\ byte = 8\ bit1 byte=8 bit

```
>>> 256*1024*1024*8//32
67108864
```

## 试题 B: 卡片

![图片说明](/images/f80f6d5174924592.png "图片标题")

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
int a[10];
bool chk(int x) {
    while (x) {
        if (a[x % 10] < 1)
            return 0;
        else
            a[x % 10] -= 1;
        x /= 10;
    }
    return 1;
}
int main() {
    rep(i, 0, 9) a[i] = 2021;
    int i = 1;
    while (chk(i)) ++i;
    cout << i - 1 << endl;  // 3181
    return 0;
}
```

## 试题 C: 直线

![图片说明](/images/0ab9e5f24e57a5c2.png "图片标题")

据说本题答案是40257

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
set<pair<double, double>> st;
const int n = 20, m = 21;
int main() {
    ll line = 0;
    for (int x1 = 0; x1 < n; ++x1) {
        ++line;                                // 单独计算竖线
        for (int x2 = x1 + 1; x2 < n; ++x2) {  // 不重复
            for (double y1 = 0; y1 < m; y1 += 1) {
                for (double y2 = 0; y2 < m; y2 += 1) {
                    double k = (y1 - y2) / (x1 - x2);
                    double b = y1 - k * x1;
                    st.insert({k, b});
                }
            }
        }
    }
    cout << st.size() + line << endl;// 41255
    return 0;
}
```

![](/images/946c0c23bd7bce07.png "图片标题")

死于double精度

## 试题 D: 货物摆放

![图片说明](/images/5249eb69f2c3584c.png "图片标题")

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
vector<ll> fac;
void div(ll n) {
    for (int i = 2, x = sqrt(n); i <= x; ++i) {
        while (n % i == 0) {
            fac.push_back(i);
            n /= i;
        }
        if (n == 1) break;
    }
}
struct node {
    ll a, b, c;
    bool operator<(node o) const {
        if (a != o.a) return a < o.a;
        if (b != o.b) return b < o.b;
        return c < o.c;
    }
};
set<node> st;
int p;
bool chk(int a, int b, int c) {
    rep(i, 1, p) {
        bool xa = a & 1;
        bool xb = b & 1;
        bool xc = c & 1;
        if (xa && xb) return 0;
        if (xa && xc) return 0;
        if (xb && xc) return 0;
        if (!xa && !xb && !xc) return 0;
        a >>= 1;
        b >>= 1;
        c >>= 1;
    }
    return 1;
}
ll cal(int sta) {
    ll res = 1;
    rep(i, 0, p - 1) {
        if (sta & 1) res *= fac[i];
        sta >>= 1;
    }
    return res;
}
int main() {
    ll n = 2021041820210418;
    div(n);
    for (int i : fac) cout << i << ' ';
    cout << '\n';
    p = fac.size();
    int maxsta = 1 << p;
    for (int i = 0; i < maxsta; ++i) {
        for (int j = 0; j < maxsta; ++j) {
            for (int k = 0; k < maxsta; ++k) {
                if (chk(i, j, k)) {
                 //   if (cal(i) * cal(j) * cal(k) == n)
                        st.insert({cal(i), cal(j), cal(k)});
                }
            }
        }
    }
    cout << st.size() << endl;  // 2430
    return 0;
}
```

## 试题 E: 路径

![图片说明](/images/da202136118dd237.png "图片标题")

```
#include <stdio.h>

#include <algorithm>
#include <queue>
using namespace std;
#define inf 2147483647
#define int long long
const int maxn = 10010, maxm = 500050;
int n, m, s;

struct edge {
    int to, weight, next;
};
edge edges[maxm];
int head[maxn], edge_cnt;

void add(int x, int y, int w) {
    edges[++edge_cnt].next = head[x];
    edges[edge_cnt].to = y;
    edges[edge_cnt].weight = w;
    head[x] = edge_cnt;
}

long long dist[maxn];
int inque[maxn];

void spfa() {
    for (int i = 1; i <= n; ++i) dist[i] = inf;
    dist[s] = 0;
    queue<int> q;
    q.push(s);
    inque[s] = 1;
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        inque[u] = 0;
        for (int i = head[u]; i != 0; i = edges[i].next) {
            int v = edges[i].to, w = edges[i].weight;
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                if (!inque[v]) {
                    q.push(v);
                    inque[v] = 1;
                }
            }
        }
    }
}

signed main() {
    n = 2021;
    s = 1;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (j > i + 21) break;
            if (abs(i - j) <= 21) add(i, j, i / __gcd(i, j) * j);
        }
    }
    spfa();
    printf("%lld ", dist[n]);  // 10266837
    return 0;
}
```

## 试题 F: 时间显示

![图片说明](/images/50d7d29a34ef2190.png "图片标题")

```
#include <bits/stdc++.h>
#define sc(x) scanf("%lld", &(x))
#define pr(x) printf("%lld\n", (x))
#define rep(i, l, r) for (int i = l; i <= r; ++i)
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const int mod = 1e9 + 7;
int main() {
    ll n;
    cin >> n;
    ll day = 24LL * 60 * 60 * 1000;
    n %= day;
    n /= 1000;
    ll s = n % 60;
    ll h = n / 3600;
    ll m = n / 60 - h * 60;
    printf("%02lld:%02lld:%02lld\n", h, m, s);
    return 0;
}
```

## 试题 G: 砝码称重

![图片说明](/images/f3759632be3584cb.png "图片标题")

小M和天平 <https://blog.nowcoder.net/n/4f630def25b94aac8d8479375f5b86e8>

```
#include<bits/stdc++.h>
#define int long long
#define rep(i,l,r) for(int i=l;i<=r;++i)
using namespace std;
const int N = 1e5+7;
bitset< N+N+10 >b;
signed main() { 
	int n,x;
	cin >> n;
	b[N]=1;
	rep(i,1,n) {
		cin >> x;
		b|=(b>>x|b<<x);
	}
	int cnt = 0;
	rep(i,N+1,N+N+9)cnt+=b[i];
	cout<<cnt<<'\n';
	return 0;
}
```

## 试题 H：杨辉三角形

![alt](/images/9b69a77ebf6318fb.png)
![alt](/images/17c6f3a3ed391e28.png)

1. 只有左半边有价值
2. 确定行号iii和列号jjj，当前位置编号即为i∗(i−1)/2+ji\*(i-1)/2+ji∗(i−1)/2+j
3. 观察每一斜线，第二斜线Cn1C\_n^1Cn1​，第三斜线Cn2C\_n^2Cn2​，第四斜线Cn3C\_n^3Cn3​，如果一个数出现在更下方的斜线上，那么它的这一位置编号必定小于高处斜线位置的编号：如6
4. C20003>1e9C\_{2000}^3>1e9C20003​>1e9，所以只需要手动处理Cn2C\_n^2Cn2​和Cn1C\_n^1Cn1​的情况即可

```
#include<bits/stdc++.h>
#define ll long long
using namespace std;
int a[2005][2005];
int main(){
	ll N;
	cin>>N;
	a[0][0]=1;
	for(int i=1;i<2005;i++){
		for(int j=1;j<=i;j++){
			a[i][j]=a[i-1][j]+a[i-1][j-1];
			if(a[i][j]==N){
				cout<<i*(i-1)/2+j<<endl;
				return 0;
			}
		}
	}
	//如果上面的没找到，说明只有C(1,n)和C(2,n)满足了
	//n*(n-1)/2==N
	ll n=sqrt(N*2)+1;
	if(n*(n-1)/2==N){
        //C(2,n)
        cout<<n*(n+1)/2+3<<endl;
	}else{
	    //C(1,n)
        cout<<N*(N+1)/2+2<<endl;
	}
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/aed7887b497e453985ed87b8bab2bdd7），发布于 2021-04-18。
