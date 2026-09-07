---
title: "递增三元组 思维 二分"
date: 2020-10-04T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解", "二分"]
---
## 题目

> 给定三个整数数组  
> A = [A1, A2, ... AN],  
> B = [B1, B2, ... BN],  
> C = [C1, C2, ... CN]，  
> 请你统计有多少个三元组(i, j, k) 满足：
>
> 1. 1 <= i, j, k <= N
> 2. Ai < Bj < Ck
>
> 【输入格式】  
> 第一行包含一个整数N。  
> 第二行包含N个整数A1, A2, ... AN。  
> 第三行包含N个整数B1, B2, ... BN。  
> 第四行包含N个整数C1, C2, ... CN。
>
> 对于30%的数据，1 <= N <= 100  
> 对于60%的数据，1 <= N <= 1000  
> 对于100%的数据，1 <= N <= 100000 0 <= Ai, Bi, Ci <= 100000
>
> 【输出格式】  
> 一个整数表示答案
>
> 【样例输入】  
> 3  
> 1 1 1  
> 2 2 2  
> 3 3 3
>
> 【样例输出】  
> 27

## 思路1

对于每一个Bi，二分地找A中比它小的有多少个，再二分地找C中比它大的有多少个，两者乘一下，就是含有Bi的组合数量。

累加即为答案。

时间复杂度 ${O(nlog(n)log(n))}$

关键在于从中间的B出发，这样就简单了很多。当然如果我把题目改成递增五元组,这种暴力方法就无法解决了。

## 代码1

```
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
using namespace std;
typedef long long ll;
const int N = 100005;
int a[N], b[N], c[N];
int main() {
    int n;
    while (cin >> n) {
        for (int i = 0; i < n; i++) scanf("%d", &a[i]);
        for (int i = 0; i < n; i++) scanf("%d", &b[i]);
        for (int i = 0; i < n; i++) scanf("%d", &c[i]);
        sort(a, a + n), sort(b, b + n), sort(c, c + n);
        ll ans = 0;
        for (int i = 0; i < n; ++i) {
            ll x = (lower_bound(a, a + n, b[i]) - a);
            ll y = n - (upper_bound(c, c + n, b[i]) - c);
            ans += x * y;
        }
        cout << ans << endl;
    }
    return 0;
}
```

## 思路2

粗略百度了一下，反正没看到和我一样的。

1. 对于每个Ai、Bi、Ci，分别标记。
2. 对三个数组混合排序。
3. 然后对这个数组扫一遍，碰到了Ai，cnt1就增加1；碰到了Bi，cnt2就增加前面遇到过的cnt1的数量（cnt2表示的其实就是合法的$(A_i,B_i)$的数量）；碰到了Ci，cnt3就增加前面累加的的cnt2
4. 答案就是cnt3

但是到这里，我忽略了一个致命的问题：假设数据相同怎么办？

举个例子：

```
3
1 2 3
1 2 3
1 2 3
```

在这种情况下，正确的程序应该输出1，也就是说，只有i=1,j=2,c=3的时候才可以。

也就是说，如果`Ai==Bj`，那么此时就不应该累加。所以在排序的时候，对于相同大小的数据，应该按照CBA的顺序排列。

## 代码2

```
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
using namespace std;
typedef long long ll;
const ll N = 3e5 + 7;
pair<ll, int> a[N];
inline bool cmp(pair<ll, int> a, pair<ll, int> b) {
    if (a.first == b.first) return a.second > b.second;//key
    return a.first < b.first;
}
int main(void) {
    ll n;
    while (~scanf("%lld", &n)) {
        for (int i = 0; i < n; ++i) scanf("%lld", &a[i].first), a[i].second = 1;
        for (int i = n; i < n * 2; ++i) scanf("%lld", &a[i].first), a[i].second = 2;
        for (int i = n * 2; i < n * 3; ++i) scanf("%lld", &a[i].first), a[i].second = 3;
        sort(a, a + n * 3, cmp);
        ll cnt1 = 0, cnt2 = 0, cnt3 = 0;
        for (int i = 0; i < n * 3; ++i) 
            if (a[i].second == 1) ++cnt1;
            else if (a[i].second == 2) cnt2 += cnt1;
            else if (a[i].second == 3) cnt3 += cnt2;
        printf("%lld\n", cnt3);
    }
    return 0;
}
```

## 五元组

![图片说明](/images/31868c63a879bf1c.png "图片标题")

```
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
using namespace std;
typedef long long ll;
const ll N = 5e5 + 7;
pair<ll, int> a[N];
inline bool cmp(pair<ll, int> a, pair<ll, int> b) {
    if (a.first == b.first) return a.second > b.second;
    return a.first < b.first;
}
int main(void) {
    ll n;
    while (~scanf("%lld", &n)) {
        for (int i = 0; i < n * 5; ++i)
            scanf("%lld", &a[i].first), a[i].second = i / n + 1;
        sort(a, a + n * 5, cmp);
        ll cnt[6] = {0};
        for (int i = 0; i < n * 5; ++i)
            if (a[i].second == 1) ++cnt[1];
            else cnt[a[i].second] += cnt[a[i].second - 1];
        printf("%lld\n", cnt[5]);
    }
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/08f99659e884485bafdd9cb031a8ca3c），发布于 2020-10-04。
