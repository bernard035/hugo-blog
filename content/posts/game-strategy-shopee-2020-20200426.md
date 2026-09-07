---
title: "Game Strategy - “Shopee杯” e起来编程暨武汉大学2020年大学生程序设计大赛决赛G题"
date: 2020-04-26T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
倒推决策树。  
cindy是没有操作空间的。所以需要枚举针对所有情况的alice/bob的选取。  
然而bob早已看穿了一切。  
然而alice早已看穿了一切的一切。

**因为信息的完全对称，先手是事实上的最终决定者。**  
谁有先手，谁有决定权。

1. c足够聪明，所以她一定会留下来最能中和的牌
2. b充分考虑到了这一点 他在所有组合结果里选择了一个最小的
3. a充分考虑到了这一点 他在所有组合结果里选择了一个最大的

```
#include<bits/stdc++.h>
using namespace std;
const int inf=0x3f3f3f3f;
int main() {
    int n;
    cin>>n;
    int a[105],b[105],c[105];
    for(int i=0; i<n; i++)cin>>a[i];
    for(int i=0; i<n; i++)cin>>b[i];
    for(int i=0; i<n; i++)cin>>c[i];
    int ans=-inf;
    int minn,cnt;
    for(int i=0; i<n; i++) {
        minn=inf;
        for(int j=0; j<n; j++) {
            cnt=inf;
            for(int k=0; k<n; k++)
                if(abs(a[i]+b[j]+c[k])<abs(cnt)) 
                    cnt=a[i]+b[j]+c[k];
            minn=min(minn,cnt);
        }
        ans=max(ans,minn);
    }
    cout<<ans<<endl;
    return 0;
}
```

题目没有卡那个如果有多个答案选择最大答案。  
其实我也没太明白什么情况下最大答案会和本答案不同。
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/91cdd07fc28149cb931d6074569a7073），发布于 2020-04-26。
