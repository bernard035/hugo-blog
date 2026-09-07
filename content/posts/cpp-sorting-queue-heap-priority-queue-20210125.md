---
title: "cpp 优先队列 大顶堆 小顶堆 自定义排序规则 匿名函数 仿函数"
date: 2021-01-25T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["学习笔记"]
---
```
int main() {
    //大顶堆
    std::priority_queue<int >q;  // 等同于 std::priority_queue<int,std::vector<int> , std::less<int> >q;
    for(int n : {1,8,5,6,3,4,0,9,7,2})
        q.push(n);
    print_queue(q);

    //小顶堆
    std::priority_queue<int,std::vector<int> , std::greater<int> >q2;
    for(int n : {1,8,5,6,3,4,0,9,7,2})
        q2.push(n);

    print_queue(q2);

    //------------------------自定义lambar比较------------------------------
// Using lambda to compare elements.
    auto cmp = [](int left, int right) { return (left ^ 1) < (right ^ 1); };
    std::priority_queue<int, std::vector<int>, decltype(cmp)> q3(cmp);

    for(int n : {1,8,5,6,3,4,0,9,7,2})
        q3.push(n);

    print_queue(q3);

}
```

## set

```
#include <bits/stdc++.h>
struct cmp {
    bool operator()(int x, int y) { return x > y; }
};
struct cmp1 {
    bool operator()(const int &x, const int &y) const { return x > y; }
};
int main() {
    set<int, cmp> st;
    st.insert(5);
    st.insert(3);
    cout << *st.begin() << '\n';
    return 0;
}
```
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/a42c2f70dd6649a792cce47b9c4d6c59），发布于 2021-01-25。
