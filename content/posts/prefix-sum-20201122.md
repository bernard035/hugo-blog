---
title: "生成平衡数组的方案数 前缀和"
date: 2020-11-22T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/题解"]
tags: ["题解"]
---
![图片说明](/images/8166db174cc9dfc9.png)

删除一个数后，这个数后面所有数都向前挪动一位，所以原本奇数位的数变成了偶数位，偶数位的数变成了奇数位。

那么对于一个删除的下标位置$i$，删除该元素后，$a_i$后面所有奇数下标元素的和其实就是移除之前，$a_i$后面所有偶数下标元素的和。

所以维护两个前缀和：奇数位和偶数位，然后模拟即可。

```
class Solution {
   public:
    int waysToMakeFair(vector<int>& nums) {
        int n = nums.size();
        vector<int> odd(n + 1, 0);
        vector<int> even(n + 1, 0);
        for (int i = 1; i <= n; i++) {
            if (i % 2 == 1) {
                odd[i] = odd[i - 1];
                even[i] = even[i - 1] + nums[i - 1];
            } else {
                odd[i] = odd[i - 1] + nums[i - 1];
                even[i] = even[i - 1];
            }
        }
        int cnt = 0;
        for (int i = 0; i < n; i++) {
            int o =
                odd[i] - odd[0] + even[n] - even[i + 1];  // 奇数下标元素之和
            int e =
                even[i] - even[0] + odd[n] - odd[i + 1];  // 偶数下标元素之和
            if (o == e) {
                cnt++;
            }
        }
        return cnt;
    }
};
```

这个代码有点难理解

```
class Solution {
public:
    int waysToMakeFair(vector<int>& a) {
        int n = a.size();
        vector<int> pre(n + 1);
        for (int i = 0; i < n; ++i) pre[i + 1] = pre[i] + (i % 2 == 0 ? a[i] : -a[i]);
        int ret = 0;
        for (int i = 0; i < n; ++i) {
            int cur = pre[i] - (pre[n] - pre[i + 1]);
            if (cur == 0) ret++;
        }
        return ret;
    }
};
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/c33118dd731d48b3b41491f6313237e4），发布于 2020-11-22。
