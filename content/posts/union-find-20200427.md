---
title: "并查集"
date: 2020-04-27T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["模板", "并查集"]
---
# 并查集简介

**0. 并查集的引入**

并查集主要用于解决一些元素分组的问题。它管理一系列**不相交**的集合，并支持两种操作：  
合并（Union）：把两个不相交的集合合并为一个集合。  
查询（Find）：查询两个元素是否在同一个集合中。

它是一种非常精巧且使用的数据结构，它主要用于处理一些不相交集合的合并问题。  
经典的例子有连通子图、最小生成树的Kruskal算法和最近公共祖先（LCA）等。

并查集的时间复杂度（不包括初始化）为 $O(log_{2}n)$

**1. 并查集的初始化**  
![初始化](/images/4d2c831ca7228660.png "图片标题")  
**2. 合并**  
![图片说明](/images/b46e1f344d628d9f.png "图片标题")  
![图片说明](/images/5803094ef70dfd58.png "图片标题")  
![图片说明](/images/413a5eb3261c6320.png "图片标题")  
**3. 查找**  
![图片说明](/images/e1a605e25b3f1bb5.png "图片标题")  
**4. 优化**  
为了解决搜索树退化问题，需要完成优化：

**本段落转自[Pecco](https://zhuanlan.zhihu.com/p/93647900)**，水印是因为牛客强制水印（差评）。

- 查找的优化：路径压缩

  ```
  int find_set(int x) {
    if (x != s[x]) s[x] = find_set(s[x]);  //路径压缩
    return s[x];
  }
  ```

  从这样![](/images/5b2ed6c379dcf031.jpg) 到这样 ![](/images/101a4ebb2a37c92c.jpg)

  - 合并的优化：按秩归并

路径压缩优化后，并查集不总是是一个菊花图（只有两层的树的俗称）。  
由于路径压缩只在查询时进行，也只压缩一条路径，所以并查集最终的结构仍然可能是比较复杂的。  
例如，现在我们有一棵较复杂的树需要与一个单元素的集合合并：  
![图片说明](/images/7203a26e23edc0f6.jpg "图片标题")

假如这时我们要merge(7,8)，如果我们可以选择的话，是把7的父节点设为8好，还是把8的父节点设为7好呢？

当然是后者。因为如果把7的父节点设为8，会使树的深度（树中最长链的长度）加深，原来的树中每个元素到根节点的距离都变长了，之后我们寻找根节点的路径也就会相应变长。虽然我们有路径压缩，但路径压缩也是会消耗时间的。而把8的父节点设为7，则不会有这个问题，因为它没有影响到不相关的节点。

![图片说明](/images/bfc9fe491d49036a.jpg "图片标题")

这启发我们：**我们应该把简单的树往复杂的树上合并，而不是相反**。因为这样合并后，到根节点距离变长的节点个数比较少。

我们用一个数组rank[]记录每个根节点对应的树的深度（如果不是根节点，其rank相当于以它作为根节点的子树的深度）。一开始，把所有元素的rank（秩）设为1。合并时比较两个根节点，把rank较小者往较大者上合并。路径压缩和按秩合并如果一起使用，时间复杂度接近 $O(n)$ ，但是很可能会破坏rank的准确性。

值得注意的是，按秩合并会带来额外的空间复杂度，可能被一些卡空间的毒瘤题卡掉。

```
void union_set(int x, int y) {
    x = find_set(x);
    y = find_set(y);
    if (x == y) return; 
    if (height[x] == height[y]) height[x] = height[x] + 1, s[y] = x;
    else if (height[x] < height[y]) s[x] = y;
    else s[y] = x;
}
```

为什么深度相同，新的根节点深度要+1？如下图，我们有两个深度均为2的树，现在要合并(2,5)：  
![图片说明](/images/abe2358d8f038882.jpg "图片标题")

这里把2的父节点设为5，或者把5的父节点设为2，其实没有太大区别。我们选择前者，于是变成这样：  
![图片说明](/images/3527896472644ac4.jpg "图片标题")  
显然树的深度增加了1。另一种合并方式同样会让树的深度+1。

# 并查集模板

## [HDU 1213](http://acm.hdu.edu.cn/showproblem.php?pid=1213)

题意：有n个人一起吃饭，有些人互相认识。认识的人想坐一起。朋友的朋友是朋友，A认识B，B认识C， ABC会坐一起（具备传递性）。给出认识的人的情况，问需要多少张桌子。

其实就是求集合数量。

**solution**

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
const double PI = acos(-1);
int height[N];
int s[N];
int n;  // point numers
void init_set() {
    for (int i = 1; i <= n; ++i) s[i] = i, height[i] = 0;
}
int find_set(int x) {
    if (x != s[x]) s[x] = find_set(s[x]);  //路径压缩
    return s[x];
}
void union_set(int x, int y) {
    x = find_set(x);
    y = find_set(y);
    if (x==y) return;
    if (height[x] == height[y]) height[x] = height[x] + 1, s[y] = x;
    else if (height[x] < height[y]) s[x] = y;
    else s[y] = x;
}
int main() {
    int T;
    cin >> T;
    int m;
    while (T--) {
        cin >> n >> m;
        init_set();
        int x, y;
        for (int i = 1; i <= m; ++i) cin >> x >> y, union_set(x, y);
        int ans = 0;
        for (int i = 1; i <= n; ++i) if (s[i] == i) ++ans;
        cout << ans << endl;
    }
    return 0;
}
```

## [POJ 2524](http://poj.org/problem?id=2524)

**Description**

There are so many different religions in the world today that it is difficult to keep track of them all. You are interested in finding out how many different religions students in your university believe in.

You know that there are n students in your university (0 < n <= 50000). It is infeasible for you to ask every student their religious beliefs. Furthermore, many students are not comfortable expressing their beliefs. One way to avoid these problems is to ask m (0 <= m <= n(n-1)/2) pairs of students and ask them whether they believe in the same religion (e.g. they may know if they both attend the same church). From this data, you may not know what each person believes in, but you can get an idea of the upper bound of how many different religions can be possibly represented on campus. You may assume that each student subscribes to at most one religion.

**Input**  
The input consists of a number of cases. Each case starts with a line specifying the integers n and m. The next m lines each consists of two integers i and j, specifying that students i and j believe in the same religion. The students are numbered 1 to n. The end of input is specified by a line in which n = m = 0.

**Output**  
For each test case, print on a single line the case number (starting with 1) followed by the maximum number of different religions that the students in the university believe in.

**Sample Input**

10 9  
1 2  
1 3  
1 4  
1 5  
1 6  
1 7  
1 8  
1 9  
1 10  
10 4  
2 3  
4 5  
4 8  
5 8  
0 0

**Sample Output**

Case 1: 1  
Case 2: 7

**Hint**  
Huge input, scanf is recommended.

题目大意：  
n个学生，m组，每一组给出相同信仰的两个人，统计宗教信仰总数。

一样是求集合数量。

**solution**

```
#include <iostream>
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
int height[N];
int s[N];
int n;  // point numers
void init_set() {
    for (int i = 1; i <= n; ++i) s[i] = i, height[i] = 0;
}
int find_set(int x) {
    if (x != s[x]) s[x] = find_set(s[x]);  //路径压缩
    return s[x];
}
int find_set_circle(int x) {
    int r = x;
    while (s[r] != r) r = s[r];  //找到根节点
    for(int i = x,j; i!=r; i = j)
        j = s[i], s[i] = r;                    //把路径上的元素的集改为根节点
    return r;
}
void union_set(int x, int y) {
    x = find_set(x);
    y = find_set(y);
    if (height[x] == height[y])
        height[x] = height[x] + 1, s[y] = x;
    else if (height[x] < height[y])
        s[x] = y;
    else
        s[y] = x;
}
int main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    int m, cnt = 0;
    while (cin >> n >> m, n || m) {
        init_set();
        int x, y;
        for (int i = 1; i <= m; ++i) cin >> x >> y, union_set(x, y);
        int ans = 0;
        for (int i = 1; i <= n; ++i) ans+=s[i]==i;
        cout << "Case " << ++cnt << ": " << ans << endl;
    }
    return 0;
}
```

## [HDU 1856](http://acm.hdu.edu.cn/showproblem.php?pid=1856)

**More is better**  
Time Limit: 5000/1000 MS (Java/Others)  
Memory Limit: 327680/102400 K (Java/Others)  
Total Submission(s): 40963  
Accepted Submission(s): 14372

**Problem Description**  
Mr Wang wants some boys to help him with a project. Because the project is rather complex, the more boys come, the better it will be. Of course there are certain requirements.

Mr Wang selected a room big enough to hold the boys. The boy who are not been chosen has to leave the room immediately. There are 10000000 boys in the room numbered from 1 to 10000000 at the very beginning. After Mr Wang's selection any two of them who are still in this room should be friends (direct or indirect), or there is only one boy left. Given all the direct friend-pairs, you should decide the best way.

**Input**  
The first line of the input contains an integer n (0 ≤ n ≤ 100 000) - the number of direct friend-pairs. The following n lines each contains a pair of numbers A and B separated by a single space that suggests A and B are direct friends. (A ≠ B, 1 ≤ A, B ≤ 10000000)

**Output**  
The output in one line contains exactly one integer equals to the maximum number of boys Mr Wang may keep.

**Sample Input**

4  
1 2  
3 4  
5 6  
1 6  
4  
1 2  
3 4  
5 6  
7 8

**Sample Output**

4  
2

**Hint**

A and B are friends(direct or indirect), B and C are friends(direct or indirect), then A and C are also friends(indirect).

In the first sample {1,2,5,6} is the result.  
In the second sample {1,2},{3,4},{5,6},{7,8} are four kinds of answers.

**题意**：求最大的集合的人数。

1e7不按秩归并436ms。

**solution**

```
#include <cstdio>
#include <algorithm>
using namespace std;
const int maxn = 10000005;
int sum[maxn];      //集合总数
int ufs[maxn];      //父节点
void Init(int n) {  //初始化
    for (int i = 1; i <= n; i++) {
        ufs[i] = i;
        sum[i] = 1;
    }
}
int Find(int a) {       //获得a的根节点。路径压缩
    if (ufs[a] != a) {  //没找到根节点
        int tmp = ufs[a];
        ufs[a] = Find(ufs[a]);
    }
    return ufs[a];
}
void Merge(int a, int b) {  //合并a和b的集合
    int x = Find(a);
    int y = Find(b);
    if (x != y) {
        ufs[x] = y;
        sum[y] += sum[x];
    }
}
int main() {
    int t, a, b;
    while (~scanf("%d", &t)) {
        Init(maxn);
        while (t--) {
            scanf("%d%d", &a, &b);
            Merge(a, b);
        }
        int ans = -1;
        for (int i = 1; i <= maxn; ++i) ans = max(ans, sum[i]);
        printf("%d\n",ans);
    }
    return 0;
}
```

## [POJ 1611](http://poj.org/problem?id=1611)

**Description**  
Severe acute respiratory syndrome (SARS), an atypical pneumonia of unknown aetiology, was recognized as a global threat in mid-March 2003. To minimize transmission to others, the best strategy is to separate the suspects from others.  
In the Not-Spreading-Your-Sickness University (NSYSU), there are many student groups. Students in the same group intercommunicate with each other frequently, and a student may join several groups. To prevent the possible transmissions of SARS, the NSYSU collects the member lists of all student groups, and makes the following rule in their standard operation procedure (SOP).  
Once a member in a group is a suspect, all members in the group are suspects.  
However, they find that it is not easy to identify all the suspects when a student is recognized as a suspect. Your job is to write a program which finds all the suspects.

**Input**  
The input file contains several cases. Each test case begins with two integers n and m in a line, where n is the number of students, and m is the number of groups. You may assume that 0 < n <= 30000 and 0 <= m <= 500. Every student is numbered by a unique integer between 0 and n−1, and initially student 0 is recognized as a suspect in all the cases. This line is followed by m member lists of the groups, one line per group. Each line begins with an integer k by itself representing the number of members in the group. Following the number of members, there are k integers representing the students in this group. All the integers in a line are separated by at least one space.  
A case with n = 0 and m = 0 indicates the end of the input, and need not be processed.

**Output**  
For each case, output the number of suspects in one line.

**Sample Input**

100 4  
2 1 2  
5 10 13 11 12 14  
2 0 1  
2 99 2  
200 2  
1 5  
5 1 2 3 4 5  
1 0  
0 0

**Sample Output**

4  
1  
1

题意：  
n个人m组，有0号在就染病毒。染病毒的人拥有无限传播性：在一组就染病毒。统计感染者数量。  
求和0号在一个集合中的人数。

**solution**

```
#include <iostream>
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
int height[N];
int s[N];
int n;  // point numers
void init_set() {
    for (int i = 0; i < n; ++i) s[i] = i, height[i] = 0;
}
int find_set(int x) {
    if (x != s[x]) s[x] = find_set(s[x]);  //路径压缩
    return s[x];
}
int find_set_circle(int x) {
    int r = x;
    while (s[r] != r) r = s[r];  //找到根节点
    for (int i = x, j; i != r; i = j) j = s[i], s[i] = r;
    return r;
}
void union_set(int x, int y) {
    x = find_set_circle(x);
    y = find_set_circle(y);
    if (height[x] == height[y])
        ++height[x], s[y] = x;
    else if (height[x] < height[y])
        s[x] = y;
    else
        s[y] = x;
}
int main() {
    ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
    int m, cnt = 0;
    while (cin >> n >> m, n || m) {
        init_set();
        int x, y;
        while (m--) {
            int k;
            cin >> k;
            cin >> x;
            while (--k) {
                cin >> y;
                union_set(x, y);
            }
        }
        int ans = 0;
        int pos = find_set(0);
        for (int i = 0; i < n; ++i) if (find_set(i) == pos) ans++;
        cout << ans << endl;
    }
    return 0;
}
```

坑点在于最后要查，不能直接用数组。

PKU模板 直接sum

```
#include <iostream>
#include <cstdio>
using namespace std;
#define MAXN 30010 
int sum[MAXN];    //集合总数
int ufs[MAXN];    //并查集

void Init(int n)    //初始化
{
    int i;
    for(i=0;i<n;i++){
        ufs[i] = i;
        sum[i] = 1;
    }
}

int GetRoot(int a)    //获得a的根节点。路径压缩
{
    if(ufs[a]!=a){    //没找到根节点
        ufs[a] = GetRoot(ufs[a]);
    }
    return ufs[a];
}

void Merge(int a,int b)    //合并a和b的集合
{
    int x = GetRoot(a);
    int y = GetRoot(b);
    if(x!=y){
        ufs[y] = x;
        sum[x] += sum[y];
    }
}

int main()
{
    int n,m;
    while(scanf("%d%d",&n,&m)!=EOF){
        if(n==0 && m==0) break;
        Init(n);    //初始化并查集
        while(m--){    //读入m行
            int t,one,two;
            scanf("%d",&t);    //每一行有t个数需要输入
            scanf("%d",&one);
            t--;
            while(t--){
                scanf("%d",&two);
                Merge(one,two);    //合并集合
            }
        }
        printf("%d\n",sum[GetRoot(0)]);
    }
    return 0;
}
```

# 并查集的简单应用

## [POJ 1703](http://poj.org/problem?id=1703)

**Description**  
The police office in Tadu City decides to say ends to the chaos, as launch actions to root up the TWO gangs in the city, Gang Dragon and Gang Snake. However, the police first needs to identify which gang a criminal belongs to. The present question is, given two criminals; do they belong to a same clan? You must give your judgment based on incomplete information. (Since the gangsters are always acting secretly.)

Assume N (N <= 10^5) criminals are currently in Tadu City, numbered from 1 to N. And of course, at least one of them belongs to Gang Dragon, and the same for Gang Snake. You will be given M (M <= 10^5) messages in sequence, which are in the following two kinds:

1. D [a] [b]  
   where [a] and [b] are the numbers of two criminals, and they belong to different gangs.

   - A [a] [b]  
     where [a] and [b] are the numbers of two criminals. This requires you to decide whether a and b belong to a same gang.

**Input**  
The first line of the input contains a single integer T (1 <= T <= 20), the number of test cases. Then T cases follow. Each test case begins with a line with two integers N and M, followed by M lines each containing one message as described above.

**Output**  
For each message "A [a] [b]" in each case, your program should give the judgment based on the information got before. The answers might be one of "In the same gang.", "In different gangs." and "Not sure yet."

**Sample Input**  
1  
5 5  
A 1 2  
D 1 2  
A 1 2  
D 2 4  
A 1 4

**Sample Output**

Not sure yet.  
In different gangs.  
In the same gang.

题意：

> 城里有两个帮派，D a b表示ab是不同的帮派，A a b要求给出A B 是否相同帮派。

手写处理逻辑，不更新对立节点。

**solution**

```
#include <cstdio>
#include <iostream>
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
int height[N];
int s[N];
int oppo[N] = {0};
int n;  // point numers
void init_set() {
    for (int i = 1; i <= n; ++i) s[i] = i, height[i] = 0, oppo[i]=0;
}
int find_set(int x) {
    if (x != s[x]) s[x] = find_set(s[x]);  //路径压缩
    return s[x];
}
void union_set(int x, int y) {
    x = find_set(x);
    y = find_set(y);
    if (height[x] == height[y])
        ++height[x], s[y] = x;
    else if (height[x] < height[y])
        s[x] = y;
    else
        s[y] = x;
}
int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        int m, a, b;
        scanf("%d%d", &n, &m);init_set();
        while (m--) {
            char x;
            getchar();
            scanf("%c %d %d", &x, &a, &b);
            if (x == 'D') {
                if (oppo[a] && !oppo[b])
                    union_set(oppo[a], b), oppo[b] = a;
                else if (oppo[b] && !oppo[a])
                    union_set(oppo[b], a), oppo[a] = b;
                else if (oppo[a] && oppo[b])
                    union_set(oppo[a], b), union_set(oppo[b], a);
                else if (!oppo[a] && !oppo[b])
                    oppo[a] = b, oppo[b] = a;
            } else {
                if (find_set(a) == find_set(oppo[b]))
                    printf("In different gangs.\n");
                else if (find_set(a) == find_set(b))
                    printf("In the same gang.\n");
                else
                    printf("Not sure yet.\n");
            }
        }
    }
    return 0;
}
```

另外要注意初始化的位置。以及0-n-1和1-n。

## [POJ 2236](http://poj.org/problem?id=2236)

**Wireless Network**  
Time Limit: 10000MS Memory Limit: 65536K  
Total Submissions: 52242 Accepted: 21322

**Description**  
An earthquake takes place in Southeast Asia. The ACM (Asia Cooperated Medical team) have set up a wireless network with the lap computers, but an unexpected aftershock attacked, all computers in the network were all broken. The computers are repaired one by one, and the network gradually began to work again. Because of the hardware restricts, each computer can only directly communicate with the computers that are not farther than d meters from it. But every computer can be regarded as the intermediary of the communication between two other computers, that is to say computer A and computer B can communicate if computer A and computer B can communicate directly or there is a computer C that can communicate with both A and B.

In the process of repairing the network, workers can take two kinds of operations at every moment, repairing a computer, or testing if two computers can communicate. Your job is to answer all the testing operations.

**Input**  
The first line contains two integers N and d (1 <= N <= 1001, 0 <= d <= 20000). Here N is the number of computers, which are numbered from 1 to N, and D is the maximum distance two computers can communicate directly. In the next N lines, each contains two integers xi, yi (0 <= xi, yi <= 10000), which is the coordinate of N computers. From the (N+1)-th line to the end of input, there are operations, which are carried out one by one. Each line contains an operation in one of following two formats:

1. "O p" (1 <= p <= N), which means repairing computer p.- "S p q" (1 <= p, q <= N), which means testing whether computer p and q can communicate.

The input will not exceed 300000 lines.

**Output**  
For each Testing operation, print "SUCCESS" if the two computers can communicate, or "FAIL" if not.

**Sample Input**

4 1  
0 1  
0 2  
0 3  
0 4  
O 1  
O 2  
O 4  
S 1 4  
O 3  
S 1 4

**Sample Output**

FAIL  
SUCCESS

题意：

> 电脑因为地震全坏了，现在要装无限网络并且修好它。  
> 第一行给出来电脑数量n和最大距离d  
> 然后每一行给出第i个电脑的坐标  
> 然后O表示修复这一台电脑，S表示查询是否两台电脑能够连接。

10s限制。之前还想了挺久的。  
一遍过了。

**solution**

```
#include <cmath>
#include <cstdio>
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
int height[N];
int s[N];
int n;  // point numers
void init_set() {
    for (int i = 1; i <= n; ++i) s[i] = i, height[i] = 0;
}
int find_set(int x) {
    if (x != s[x]) s[x] = find_set(s[x]);  //路径压缩
    return s[x];
}
int find_set_circle(int x) {
    int r = x;
    while (s[r] != r) r = s[r];  //找到根节点
    s[x] = r;                    //把路径上的元素的集改为根节点
    return r;
}
void union_set(int x, int y) {
    x = find_set(x);
    y = find_set(y);
    if (height[x] == height[y])
        height[x] = height[x] + 1, s[y] = x;
    else if (height[x] < height[y])
        s[x] = y;
    else
        s[y] = x;
}
struct point {
    double x, y;
} a[N];
double getdis(point a, point b) {
    return sqrt((a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y));
}
int main() {
    int able[N] = {0};
    int cnt = 0;
    double d;
    scanf("%d%lf", &n, &d);
    init_set();
    for (int i = 1; i <= n; ++i) scanf("%lf%lf", &a[i].x, &a[i].y);
    char op;
    int p, q;
    while (~scanf("\n%c%d", &op, &p)) {
        if (op == 'O') {
            for (int i = 0; i < cnt; ++i)
                if (getdis(a[able[i]], a[p]) <= d) union_set(able[i], p);
            able[cnt++] = p;
        } else {
            scanf("%d", &q);
            if (find_set(p) == find_set(q))
                printf("SUCCESS\n");
            else
                printf("FAIL\n");
        }
    }
    return 0;
}
```

## [POJ 2492](http://poj.org/problem?id=2492)

**A Bug's Life**  
Time Limit: 10000MS Memory Limit: 65536K  
Total Submissions: 53098 Accepted: 17072

**Description**  
*Background*  
Professor Hopper is researching the sexual behavior of a rare species of bugs. He assumes that they feature two different genders and that they only interact with bugs of the opposite gender. In his experiment, individual bugs and their interactions were easy to identify, because numbers were printed on their backs.  
*Problem*  
Given a list of bug interactions, decide whether the experiment supports his assumption of two genders with no homosexual bugs or if it contains some bug interactions that falsify it.

**Input**  
The first line of the input contains the number of scenarios. Each scenario starts with one line giving the number of bugs (at least one, and up to 2000) and the number of interactions (up to 1000000) separated by a single space. In the following lines, each interaction is given in the form of two distinct bug numbers separated by a single space. Bugs are numbered consecutively starting from one.

**Output**  
The output for every scenario is a line containing "Scenario #i:", where i is the number of the scenario starting at 1, followed by one line saying either "No suspicious bugs found!" if the experiment is consistent with his assumption about the bugs' sexual behavior, or "Suspicious bugs found!" if Professor Hopper's assumption is definitely wrong.

**Sample Input**

2  
3 3  
1 2  
2 3  
1 3  
4 2  
1 2  
3 4

**Sample Output**

Scenario #1:  
Suspicious bugs found!

Scenario #2:  
No suspicious bugs found!

**Hint**  
Huge input,scanf is recommended.

题意：教授提出猜想，所有虫子都是异性恋，给出交配情况，判断他的猜想是否正确（肯定不正确啊）

和之前的分帮派很像。

这次10s的限制在766ms跑完了。

**solution**

```
#include <cmath>
#include <cstdio>
typedef long long ll;
const int N = 1e5 + 7;
const ll mod = 1e9 + 7;
int height[N];
int s[N];
int n;  // point numers
void init_set() {
    for (int i = 1; i <= n; ++i) s[i] = i, height[i] = 0;
}
int find_set(int x) {
    if (x != s[x]) s[x] = find_set(s[x]);  //路径压缩
    return s[x];
}
int find_set_circle(int x) {
    int r = x;
    while (s[r] != r) r = s[r];  //找到根节点
    s[x] = r;                    //把路径上的元素的集改为根节点
    return r;
}
void union_set(int x, int y) {
    x = find_set(x);
    y = find_set(y);
    if (height[x] == height[y])
        height[x] = height[x] + 1, s[y] = x;
    else if (height[x] < height[y])
        s[x] = y;
    else
        s[y] = x;
}
int main() {
    int T;
    int m;
    scanf("%d", &T);
    for (int t = 1; t <= T; ++t) {
        scanf("%d%d", &n, &m);
        init_set();
        int oppo[N] = {0};
        int a, b;
        bool flag = 1;
        while (m--) {
            scanf("%d%d", &a, &b);
            if (!flag) continue;
            if (oppo[a] && !oppo[b]) {
                union_set(oppo[a], b), oppo[b] = a;
            } else if (oppo[b] && !oppo[a]) {
                union_set(oppo[b], a), oppo[a] = b;
            } else if (oppo[a] && oppo[b]) {
                if (find_set(a) == find_set(b)) flag = 0;
                union_set(oppo[a], b), union_set(oppo[b], a);
            } else if (!oppo[a] && !oppo[b]) {
                oppo[a] = b, oppo[b] = a;
            }
        }
        if (t != 1) printf("\n");
        printf("Scenario #%d:\n", t);
        if (flag)
            printf("No suspicious bugs found!\n");
        else
            printf("Suspicious bugs found!\n");
    }
    return 0;
}
```

## [人人都是好朋友](https://ac.nowcoder.com/acm/contest/5158/H)

并查集+离散化

```
#include <bits/stdc++.h>
using namespace std;
#define js ios::sync_with_stdio(false);cin.tie(0); cout.tie(0)
typedef long long ll;

inline int read() {
    int s = 0, w = 1; char ch = getchar();
    while (ch < 48 || ch > 57) { if (ch == '-') w = -1; ch = getchar(); }
    while (ch >= 48 && ch <= 57) s = (s << 1) + (s << 3) + (ch ^ 48), ch = getchar();
    return s * w;
}

const int N = 2e6 + 7;
int fa[N];
int a[N], b[N], c[N];
vector<int> p;

int find(int x) {
    return fa[x] == x ? x : fa[x] = find(fa[x]);
}

int main() {
    int T = read();
    while (T--) {
        int n = read();

        //初始化
        p.clear();
        for (int i = 1; i <= 2 * n; ++i) fa[i] = i;
        for (int i = 1; i <= n; ++i) {
            a[i] = read(), b[i] = read(), c[i] = read();
            p.push_back(a[i]);
            p.push_back(b[i]);
        }

        //离散化
        sort(p.begin(), p.end());
        p.erase(unique(p.begin(), p.end()), p.end()); //去重
        for (int i = 1; i <= n; ++i) {
            a[i] = lower_bound(p.begin(), p.end(), a[i]) - p.begin() + 1;
            b[i] = lower_bound(p.begin(), p.end(), b[i]) - p.begin() + 1;
        }

        //并查集
        for (int i = 1; i <= n; ++i) {
            if (c[i]) {
                int A = find(a[i]), B = find(b[i]);
                fa[B] = A;
            }
        }
        bool flag = true;
        for (int i = 1; i <= n; ++i)
            if (!c[i]) {
                int A = find(a[i]), B = find(b[i]);
                if (A == B) {
                    flag = false;
                    break;
                }
            }
        if (flag)   puts("YES");
        else    puts("NO");
    }
    return 0;
}
```

用取模完成离散化。

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 2e6 + 3;
int Level[N];
int fa[N];
int a[N], b[N], c[N];
int n;  // point numers
inline void INIT() {
    for (int i = 0; i <= N; ++i) fa[i] = i, Level[i] = 0;
}
int Find(int x) {
    int r = x;
    while (fa[r] != r) r = fa[r];                            //找到根节点
    for (int i = x, j; i != r; i = j) j = fa[i], fa[i] = r;  //路径压缩
    return r;
}
void Union(int x, int y) {
    x = Find(x);
    y = Find(y);
    if (Level[x] == Level[y])
        Level[x] = Level[x] + 1, fa[y] = x;
    else if (Level[x] < Level[y])
        fa[x] = y;
    else
        fa[y] = x;
}
int main() {
    int T;
    cin >> T;
    while (T--) {
        INIT();
        cin >> n;
        int cnt = 0;
        for (int i = 0; i < n; ++i) {
            cin >> a[i] >> b[i] >> c[i];
            a[i] %= N;
            b[i] %= N;
        }
        int d[n + 2][2];
        int L = 0;
        for (int i = 0; i < n; ++i) {
            if (c[i])
                Union(a[i], b[i]);
            else
                d[L][0] = a[i], d[L++][1] = b[i];
        }
        bool f = true;
        for (int i = 0; i < L; ++i)
            if (Find(d[i][0]) == Find(d[i][1])) {
                f = false;
                break;
            }
        if (f)
            cout << "YES" << endl;
        else
            cout << "NO" << endl;
    }
    return 0;
}
```

# 活用并查集

## [HDU 1198](http://acm.hdu.edu.cn/showproblem.php?pid=1198)

把图构建出来扫一遍就可以了，搜索右下是否连通，加入集合，查询集合数量。水题。

**solution**

```
#include <cstdio>
#include <cstring>
#include <iostream>
#include <string>
#include <vector>
using namespace std;
const int maxn = 2510;
char g[55][55];
int dir[4][2] = {0, 1, 0, -1, 1, 0, -1, 0};  // r,l,d,u
int a[maxn];
int cnt[maxn];
int n, m;
struct Type {
    char c;
    int left = 0;
    int right = 0;
    int top = 0;
    int down = 0;
    Type() {}
    Type(char c) : c(c) {
        switch (c) {
            case 'A':
                left = 1;
                top = 1;
                break;
            case 'B':
                right = 1;
                top = 1;
                break;
            case 'C':
                left = 1;
                down = 1;
                break;
            case 'D':
                right = 1;
                down = 1;
                break;
            case 'E':
                top = 1;
                down = 1;
                break;
            case 'F':
                left = 1;
                right = 1;
                break;
            case 'G':
                left = 1;
                right = 1;
                top = 1;
                break;
            case 'H':
                top = 1;
                left = 1;
                down = 1;
                break;
            case 'I':
                left = 1;
                right = 1;
                down = 1;
                break;
            case 'J':
                top = 1;
                down = 1;
                right = 1;
                break;
            case 'K':
                top = 1;
                down = 1;
                left = 1;
                right = 1;
                break;
        }
    }
};
Type node[55][55];
int Find(int v) {
    if (v == a[v])
        return a[v];
    else
        return Find(a[v]);
}
void UN(int p, int q) {
    int r, s;
    r = Find(p);
    s = Find(q);
    if (r != s) {
        // union q,p by union the root of p or the root of q
        if (cnt[r] < cnt[s]) {
            a[r] = a[s];  //
            cnt[s] += cnt[r];
        } else {
            a[s] = a[r];

            cnt[r] += cnt[s];
        }
    }
}
int main() {
    string s;
    while (scanf("%d%d", &m, &n) != EOF) {
        if (m < 0 && n < 0) break;

        for (int i = 0; i < m; i++) {
            cin >> s;
            for (int j = 0; j < n; j++) {
                g[i][j] = s[j];
                // init the union find
                int t = i * n + j;
                a[t] = t;
                cnt[t] = 1;
                node[i][j] = Type(s[j]);
            }
        }
        // build the map
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int xx, yy;
                // we just need charge two direction the right and the down
                // right
                xx = i;
                yy = j + 1;
                if (yy < n) {
                    if (node[i][j].right == 1 && node[xx][yy].left == 1)
                        UN(i * n + j, xx * n + yy);
                }
                // down
                xx = i + 1;
                yy = j;
                if (xx < m) {
                    if (node[i][j].down == 1 && node[xx][yy].top == 1)
                        UN(i * n + j, xx * n + yy);
                }
            }
        }
        int ans = 0;
        for (int i = 0; i < m * n; i++) {
            if (a[i] == i) ans++;
        }
        printf("%d\n", ans);
    }
    return 0;
}
```

## [HDU 2586](http://acm.hdu.edu.cn/showproblem.php?pid=2586)

[LCA 的题](https://blog.csdn.net/qq_40924940/article/details/84715751)，DFS+并查集也可以做，删掉两个优化，树的直接表示，然后直接计算两点到根的距离。

杨文冠的代码

```
#include<iostream>
#include<cstdio>
#define  js  ios::sync_with_stdio(false)
using namespace std;
typedef long long ll;
int fa[40005],size[40005],ans1=0,ans2=0;
inline init(int x){
    for(int i=1;i<=x;++i) {
        fa[i]=i;
        size[i]=0;
    }
}
void merge(int i,int j,int d) {
    if(size[i]) {
        fa[j]=i;
        size[j]=d;
    }
    else {
        fa[i]=j;
        size[i]=d;
    }
}
int find1(int x){
    if(fa[x]==x)    return ans1;
    ans1+=size[x];
    find1(fa[x]);
}
int find2(int x){
    if(fa[x]==x)    return ans2;
    ans2+=size[x];
    find2(fa[x]);
}
int main() {
    int t,n,m;
    js;
    cin>>t;
    while(t--) {
        int a,b,c;
        cin>>n>>m;
        init(n);
        for(int i=1;i<=n-1;++i) {
            cin>>a>>b>>c;
            merge(a,b,c);
        }
        for(int i=1;i<=m;++i) {
            cin>>a>>b;
            ans1=ans2=0;
            cout<<abs(find1(a)-find2(b))<<endl;
        }
    }
}
```

## [HDU 1272](http://acm.hdu.edu.cn/showproblem.php?pid=1272)

**小希的迷宫**  
Time Limit: 2000/1000 MS (Java/Others)  
Memory Limit: 65536/32768 K (Java/Others)  
Total Submission(s): 80301  
 Accepted Submission(s): 25281

**Problem Description**  
上次Gardon的迷宫城堡小希玩了很久（见Problem B），现在她也想设计一个迷宫让Gardon来走。但是她设计迷宫的思路不一样，首先她认为所有的通道都应该是双向连通的，就是说如果有一个通道连通了房间A和B，那么既可以通过它从房间A走到房间B，也可以通过它从房间B走到房间A，为了提高难度，小希希望任意两个房间有且仅有一条路径可以相通（除非走了回头路）。小希现在把她的设计图给你，让你帮忙判断她的设计图是否符合她的设计思路。比如下面的例子，前两个是符合条件的，但是最后一个却有两种方法从5到达8。

![maze](/images/90b3668355ffcdda.jpg)

**Input**  
输入包含多组数据，每组数据是一个以0 0结尾的整数对列表，表示了一条通道连接的两个房间的编号。房间的编号至少为1，且不超过100000。每两组数据之间有一个空行。  
整个文件以两个-1结尾。

**Output**  
对于输入的每一组数据，输出仅包括一行。如果该迷宫符合小希的思路，那么输出"Yes"，否则输出"No"。

**Sample Input**

6 8 5 3 5 2 6 4  
5 6 0 0

8 1 7 3 6 2 8 9 7 5  
7 4 7 8 7 6 0 0

3 8 6 8 6 4  
5 3 5 6 5 2 0 0

-1 -1

**Sample Output**

Yes  
Yes  
No

**Source**  
[HDU 2006-4 Programming Contest](http://acm.hdu.edu.cn/search.php?field=problem&key=HDU+2006-4+Programming+Contest+&source=1&searchmode=source)

题意：判断图是否连通且不成环。

这题是真的给我写吐了。  
很简单的题。  
我一开始没看到连通。  
然后因为魔性的输入方式wa了好几次。

**solution**

```
#include <cstdio>
#include <set>
using namespace std;
const int N = 100000 + 5;
bool circle;  //判断是否形成个环
int fa[N];
/*int find(int x)//爆栈就是在这里
{
    if(fa[x]!=x) fa[x]=find(fa[x]);
    return fa[x];
}*/
int find(int x) {
    while (fa[x] != x) x = fa[x];
    return fa[x];
}
void uni(int x, int y) {
    int xx = find(x);
    int yy = find(y);
    if (xx != yy)
        fa[xx] = yy;
    else
        circle = true;
}
void init() {
    for (int i = 1; i < N; i++) fa[i] = i;
}
int main() {
    set<int> s;  //至于这个set容器可以用来找顶点，在这个容器里重复的数字只计一次
    int a, b, sum;  // sum计算边
    while (scanf("%d %d", &a, &b) != EOF) {
        init();
        sum = 1, circle = false;
        if (a == 0 && b == 0)  //这也是一个比较坑的地方
        {
            printf("Yes\n");
            continue;
        }
        if (a == -1 && b == -1) break;
        s.insert(a);
        s.insert(b);
        if (!circle) uni(a, b);
        while (1) {
            scanf("%d %d", &a, &b);
            if (a == 0 && b == 0) break;
            s.insert(a), s.insert(b);
            if (!circle) uni(a, b);
            sum++;
        }
        if (!circle && s.size() == sum + 1)
            printf("Yes\n");
        else
            printf("No\n");
        s.clear();
    }
    return 0;
}
```

## [HDU 1325](http://acm.hdu.edu.cn/showproblem.php?pid=1325)

Time Limit: 2000/1000 MS (Java/Others)  
Memory Limit: 65536/32768 K (Java/Others)  
Total Submission(s): 36740  
Accepted Submission(s): 8449

**Problem Description**  
A tree is a well-known data structure that is either empty (null, void, nothing) or is a set of one or more nodes connected by directed edges between nodes satisfying the following properties.  
There is exactly one node, called the root, to which no directed edges point.

Every node except the root has exactly one edge pointing to it.

There is a unique sequence of directed edges from the root to each node.

For example, consider the illustrations below, in which nodes are represented by circles and edges are represented by lines with arrowheads. The first two of these are trees, but the last is not.

![eg.](/images/11e8d6adaef89e3e.gif)

In this problem you will be given several descriptions of collections of nodes connected by directed edges. For each of these you are to determine if the collection satisfies the definition of a tree or not.

**Input**  
The input will consist of a sequence of descriptions (test cases) followed by a pair of negative integers. Each test case will consist of a sequence of edge descriptions followed by a pair of zeroes Each edge description will consist of a pair of integers; the first integer identifies the node from which the edge begins, and the second integer identifies the node to which the edge is directed. Node numbers will always be greater than zero.

**Output**  
For each test case display the line `Case k is a tree." or the line`Case k is not a tree.", where k corresponds to the test case number (they are sequentially numbered starting with 1).

**solution**

**Sample Input**

6 8 5 3 5 2 6 4  
5 6 0 0  
8 1 7 3 6 2 8 9 7 5  
7 4 7 8 7 6 0 0  
3 8 6 8 6 4  
5 3 5 6 5 2 0 0  
-1 -1

**Sample Output**

Case 1 is a tree.  
Case 2 is a tree.  
Case 3 is not a tree.

**Source**  
[North Central North America 1997](http://acm.hdu.edu.cn/search.php?field=problem&key=North+Central+North+America+1997&source=1&searchmode=source)

[HDU 1272](http://acm.hdu.edu.cn/showproblem.php?pid=1272)的有向图升级版

并查集的写法。

```
#include <algorithm>
#include <cstdio>
#include <cstring>
const int N = 10005;
int fa[N], vis[N], maxn;
void init() {
    for (int i = 1; i < N; i++) fa[i] = i;
    memset(vis, 0, sizeof(vis));
    maxn = 0;
}
int find(int x) { return x == fa[x] ? x : (fa[x] = find(fa[x])); }
int merge(int i, int j, int flag) {
    int x = find(i), y = find(j);
    if (x == y || y != j) return 0;  //入度不为1
    fa[y] = x;
    return flag;
}
int main() {
    int a, b, j = 1, flag = 1;
    init();
    while (~scanf("%d%d", &a, &b) && a != -1) {
        if (!a) {
            int ans = 0;
            for (int i = 1; i <= maxn; i++)
                if (vis[i] && i == fa[i]) ans++;
            if (ans <= 1 && flag)
                printf("Case %d is a tree.\n", j++);
            else
                printf("Case %d is not a tree.\n", j++);
            init();
            flag = 1;
            continue;
        }
        vis[a] = vis[b] = 1;
        maxn = max(maxn, max(a, b));
        flag = merge(a, b, flag);
    }
    return 0;
}
```

树的写法

```
#include <stdio.h>
const int max_num = 100000 + 10;
typedef struct {
    int num, root, conn;  //数据、根、入度
} Node;
Node node[max_num];
void init() {
    for (int i = 0; i < max_num; i++) {
        node[i].conn = 0;  //入度初始化为0
        node[i].root = i;  //根记录为自身
        node[i].num = 0;  //标记数字是否被使用过，0:没有被使用过，1:使用过了
    }
}
int find_root(int a) {
    if (node[a].root != a) return node[a].root = find_root(node[a].root);
    return node[a].root;
}
void union_set(int a, int b) {
    a = find_root(a);
    b = find_root(b);
    if (a == b)  //同一个根，说明是在同一个树下
        return;
    node[b].root = a;  //把b的根赋为a的根，此时a已经是根，num==root
}
int main() {
    int n, m;
    int i = 1;
    bool flag = true;  // true：是个树，false:不是树
    init();
    while (scanf("%d%d", &n, &m) != EOF && n >= 0 && m >= 0) {
        if (!flag && n != 0 && n != 0) continue;  //已经确定不是树了，就继续循环
        if (n == 0 && m == 0) {
            int root_num = 0;
            for (int j = 1; j < max_num; j++) {
                //判断是否为森林，如果，root_num用来记录根的数目
                if (node[j].num && find_root(j) == j) root_num++;
                if (node[j].conn > 1)  //如果出现某个节点的入度超过1，不是树
                {
                    flag = false;
                    break;
                }
            }
            if (root_num > 1)  //连通分支大于1，是森林不是树
                flag = false;
            if (flag)
                printf("Case %d is a tree.\n", i++);
            else
                printf("Case %d is not a tree.\n", i++);
            flag = true;
            init();
            continue;
        }
        if (m != n && find_root(n) == find_root(m))
            flag = false;
        else {
            //将m,n，记录为节点
            node[m].num = 1;
            node[n].num = 1;
            node[m].conn++;  //入度增加一
            union_set(n, m);
        }
    }
    return 0;
}
```

## [HDU 6109](http://acm.hdu.edu.cn/showproblem.php?pid=6109)

**数据分割**

Time Limit: 2000/1000 MS (Java/Others)  
Memory Limit: 32768/32768 K (Java/Others)  
Total Submission(s): 2463  
Accepted Submission(s): 751

**Problem Description**

小w来到百度之星的赛场上，准备开始实现一个程序自动分析系统。

这个程序接受一些形如xi=xj 或 xi≠xj 的相等/不等约束条件作为输入，判定是否可以通过给每个 w 赋适当的值，来满足这些条件。

输入包含多组数据。  
然而粗心的小w不幸地把每组数据之间的分隔符删掉了。  
他只知道每组数据都是不可满足的，且若把每组数据的最后一个约束条件去掉，则该组数据是可满足的。

请帮助他恢复这些分隔符。

**Input**

第1行：一个数字L，表示后面输入的总行数。

之后L行，每行包含三个整数，$i,j,e$，描述一个相等/不等的约束条件，若$e=1$，则该约束条件为$x_i=x_j$ ，若e=0，则该约束条件为 $x_i≠x_j$ 。

$$i,j,L≤100000$$

$$x_i,x_j≤L$$

**Output**

输出共T+1行。

第一行一个整数T，表示数据组数。

接下来T行的第i行，一个整数，表示第i组数据中的约束条件个数。

**Sample Input**

6  
2 2 1  
2 2 1  
1 1 1  
3 1 1  
1 3 1  
1 3 0

**Sample Output**

1  
6

**Source**  
[2017"百度之星"程序设计大赛 - 初赛（A）](http://acm.hdu.edu.cn/search.php?field=problem&key=2017%26quot%3B%B0%D9%B6%C8%D6%AE%D0%C7%26quot%3B%B3%CC%D0%F2%C9%E8%BC%C6%B4%F3%C8%FC+-+%B3%F5%C8%FC%A3%A8A%A3%A9&source=1&searchmode=source)

**题意：**

给L个条件，这L个条件可以分成N段，每一段有且仅有最后一条信息和之前的信息冲突（错误信息）。求段落数量，和每一段的信息总数。

错误：单纯的并查集，即维护对立关系和两倍数组不可行，因为敌人的敌人不一定是朋友，即不等于不具备传递性。

**并查集+set**

开1e5个set

1. 对于*等于*的条件并查集- 对于*不等于*的条件存在set里面- 并查集合并的时候也要对set启发式合并

**离线倍增**

如果我们只考虑一段[l..r]是否可行，那么我们可以离线，先挑出相同的条件构出并查集，然后对所有的不同条件进行判断就行了

这样我们就可以写出一个时间复杂度为$O(r-l)$的$check(l,r)$

那我们怎么分段呢？

我们对于当前起点now，进行倍增寻找长度最小的L(L=2^k)使得[now...now+L]这一段不能构成一段

然后我们再对[now..now+L]进行二分寻找准确的分界点

时间复杂度$O(nlogn)$

**solution**

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 1e5 + 7;
int height[N];
int s[N];
set<int> unequal[N];
inline void init_set() {
    for (int i = 0; i <= N; ++i) s[i] = i, height[i] = 0;
}
int find_set(int x) {
    if (x != s[x]) s[x] = find_set(s[x]);  //路径压缩
    return s[x];
}
void union_set(int x, int y) {
    x = find_set(x);
    y = find_set(y);
    if (height[x] == height[y])
        height[x] = height[x] + 1, s[y] = x,
        unequal[x].insert(unequal[y].begin(), unequal[y].end());
    else if (height[x] < height[y])
        s[x] = y, unequal[y].insert(unequal[x].begin(), unequal[x].end());
    else
        s[y] = x, unequal[x].insert(unequal[y].begin(), unequal[y].end());
}
int L, cnt = 0;
vector<int> ans;
set<int> vis;
void restart() {
    ans.push_back(cnt);
    cnt = 0;
    for (auto i : vis) s[i] = i, unequal[i].clear();
    vis.clear();
}
int main() {
    int a, b, op;
    cin >> L;
    init_set();
    while (L--) {
        cin >> a >> b >> op;
        vis.insert(a), vis.insert(b);
        ++cnt;
        int aa = find_set(a), bb = find_set(b);
        if (op) {
            if (unequal[aa].find(bb) != unequal[aa].end() ||
                unequal[bb].find(aa) !=
                    unequal[bb].end())  //两个点已不相等，冲突
                restart();
            else if (aa != bb)
                union_set(aa, bb);
        } else {
            if (aa == bb)  //两个点已相等，冲突
                restart();
            else {
                unequal[aa].insert(bb);
                unequal[bb].insert(aa);
            }
        }
    }
    cout << ans.size() << endl;
    for (int i : ans) cout << i << endl;
    return 0;
}
```

吐槽一下，这道题其实说不上是什么对并查集的复杂应用。其实是非常简单的。

难点就在于不相等情况是不能用并查集来解决的。

这道题也用不上所谓的启发式合并，不管是对set启发式（大合并小）还是按秩归并，都不影响结果。

需要注意的点就在于，只对根操作，合并set/并查集都是如此。

我浪费这么长时间只是因为初始化写错了= =

# 高级并查集

## [POJ 1988](http://poj.org/problem?id=1988)

**Cube Stacking**  
Time Limit: 2000MS Memory Limit: 30000K  
Total Submissions: 31085 Accepted: 10931  
Case Time Limit: 1000MS

**Description**  
Farmer John and Betsy are playing a game with N (1 <= N <= 30,000)identical cubes labeled 1 through N. They start with N stacks, each containing a single cube. Farmer John asks Betsy to perform P (1<= P <= 100,000) operation. There are two types of operations:  
moves and counts.

- In a move operation, Farmer John asks Bessie to move the stack containing cube X on top of the stack containing cube Y.- In a count operation, Farmer John asks Bessie to count the number of cubes on the stack with cube X that are under the cube X and report that value.

Write a program that can verify the results of the game.

**Input**

- Line 1: A single integer, P

  - Lines 2..P+1: Each of these lines describes a legal operation. Line 2 describes the first operation, etc. Each line begins with a 'M' for a move operation or a 'C' for a count operation. For move operations, the line also contains two integers: X and Y.For count operations, the line also contains a single integer: X.

Note that the value for N does not appear in the input file. No move operation will request a move a stack onto itself.

**Output**  
Print the output from each of the count operations in the same order as the input file.

**Sample Input**

6  
M 1 6  
C 1  
M 2 4  
M 2 6  
C 3  
C 4

**Sample Output**

1  
0  
2

题意：

> 有30000个栈，每个栈上面放着一个盒子（带编号）。  
> M a b表示把a所在栈的所有盒子整体移动到b上  
> C a 表示查询a身下有多少个盒子

更新本节点到根节点的逻辑距离。

**solution**

```
#include <cstdio>
#include <iostream>
using namespace std;
const int MAXN = 30010;
int sum[MAXN];  //集合总数
int ufs[MAXN];  //并查集
int height[MAXN];
void Init() {  //初始化
    int i;
    for (i = 1; i <= MAXN; i++) {
        ufs[i] = i;
        sum[i] = 1;
        height[i] = 0;
    }
}
int Find(int a) {       //获得a的根节点。路径压缩
    if (ufs[a] != a) {  //没找到根节点
        int tmp = ufs[a];
        ufs[a] = Find(ufs[a]);
        height[a] += height[tmp];
    }
    return ufs[a];
}
void Merge(int a, int b) {  //合并a和b的集合
    int x = Find(a);
    int y = Find(b);
    if (x != y) {
        ufs[x] = y;
        height[x] = sum[y];
        sum[y] += sum[x];
    }
}
int main() {
    int n;
    while (~scanf("%d", &n)) {
        Init();
        char op;
        int x, y;
        for (int cas = 1; cas <= n; ++cas) {
            scanf(" %c%d", &op, &x);
            if (op == 'M') {
                scanf("%d", &y);
                Merge(x, y);
            } else if (op == 'C') {
                Find(x);
                printf("%d\n", height[x]);
            }
        }
    }
    return 0;
}
```

## [HDU 3635](http://acm.hdu.edu.cn/showproblem.php?pid=3635)

**Dragon Balls**  
Time Limit: 2000/1000 MS (Java/Others) Memory Limit: 32768/32768 K (Java/Others)  
Total Submission(s): 10917 Accepted Submission(s): 3880

**Problem Description**  
Five hundred years later, the number of dragon balls will increase unexpectedly, so it's too difficult for Monkey King(WuKong) to gather all of the dragon balls together.

![wukong](/images/bc8d5428e75a5194.jpg)

His country has N cities and there are exactly N dragon balls in the world. At first, for the ith dragon ball, the sacred dragon will puts it in the ith city. Through long years, some cities' dragon ball(s) would be transported to other cities. To save physical strength WuKong plans to take Flying Nimbus Cloud, a magical flying cloud to gather dragon balls.  
Every time WuKong will collect the information of one dragon ball, he will ask you the information of that ball. You must tell him which city the ball is located and how many dragon balls are there in that city, you also need to tell him how many times the ball has been transported so far.

**Input**  
The first line of the input is a single positive integer T(0 < T <= 100).  
For each case, the first line contains two integers: N and Q (2 < N <= 10000 , 2 < Q <= 10000).  
Each of the following Q lines contains either a fact or a question as the follow format:  
 T A B : All the dragon balls which are in the same city with A have been transported to the city the Bth ball in. You can assume that the two cities are different.  
 Q A : WuKong want to know X (the id of the city Ath ball is in), Y (the count of balls in Xth city) and Z (the tranporting times of the Ath ball). (1 <= A, B <= N)

**Output**  
For each test case, output the test case number formated as sample output. Then for each query, output a line with three integers X Y Z saparated by a blank space.

**Sample Input**

2  
3 3  
T 1 2  
T 3 2  
Q 2  
3 4  
T 1 2  
Q 1  
T 1 3  
Q 1

**Sample Output**

Case 1:  
2 3 0  
Case 2:  
2 2 1  
3 3 2

**题意**：  
N个龙珠Q次操作，T a b表示把a号龙珠所在城市的所有龙珠移动到b号龙珠所在城市，Q a表示询问a号龙珠所在城市，该城市所有龙珠数量以及a龙珠的移动次数。

高级并查集。和农夫盒子的那道一样。

**solution**

```
#include <cstdio>
#include <iostream>
using namespace std;
const int maxn = 10010;
int sum[maxn];    //集合总数
int ufs[maxn];    //父节点
int times[maxn];  //移动次数
void Init(int n) {     //初始化
    for (int i = 1; i <= n; i++) {
        ufs[i] = i;
        sum[i] = 1;
        times[i] = 0;
    }
}
int Find(int a) {       //获得a的根节点。路径压缩
    if (ufs[a] != a) {  //没找到根节点
        int tmp = ufs[a];
        ufs[a] = Find(ufs[a]);
        times[a] += times[tmp];
    }
    return ufs[a];
}
void Merge(int a, int b) {  //合并a和b的集合
    int x = Find(a);
    int y = Find(b);
    if (x != y) {
        ufs[x] = y;
        times[x]++;
        sum[y] += sum[x];
    }
}
int main() {
    int T, m, n;
    scanf("%d", &T);
    for (int ca = 1; ca <= T; ++ca) {
        scanf("%d%d", &n, &m);
        Init(n);
        char op;
        int x, y;
        printf("Case %d:\n",ca);
        while(m--) {
            scanf(" %c%d", &op, &x);
            if (op == 'T') {
                scanf("%d", &y);
                Merge(x, y);
            } else if (op == 'Q') {
                int ans=Find(x);
                printf("%d %d %d\n",ans , sum[ans], times[x]);
            }
        }
    }
    return 0;
}
```

printf不会按照逗号顺序执行函数，是并行的。

# 带权的并查集

## [POJ 1182](http://poj.org/problem?id=1182)

**食物链**  
Time Limit: 1000MS  
Memory Limit: 10000K  
Total Submissions: 116097  
Accepted: 35338

**Description**  
动物王国中有三类动物A,B,C，这三类动物的食物链构成了有趣的环形。A吃B， B吃C，C吃A。  
现有N个动物，以1－N编号。每个动物都是A,B,C中的一种，但是我们并不知道它到底是哪一种。  
有人用两种说法对这N个动物所构成的食物链关系进行描述：  
第一种说法是"1 X Y"，表示X和Y是同类。  
第二种说法是"2 X Y"，表示X吃Y。  
此人对N个动物，用上述两种说法，一句接一句地说出K句话，这K句话有的是真的，有的是假的。  
当一句话满足下列三条之一时，这句话就是假话，否则就是真话。  
1） 当前的话与前面的某些真的话冲突，就是假话；  
2） 当前的话中X或Y比N大，就是假话；  
3） 当前的话表示X吃X，就是假话。  
你的任务是根据给定的N（1 <= N <= 50,000）和K句话（0 <= K <= 100,000），输出假话的总数。

**Input**  
第一行是两个整数N和K，以一个空格分隔。  
以下K行每行是三个正整数 D，X，Y，两数之间用一个空格隔开，其中D表示说法的种类。  
若D=1，则表示X和Y是同类。  
若D=2，则表示X吃Y。

**Output**  
只有一个整数，表示假话的数目。

**Sample Input**

100 7  
1 101 1  
2 1 2  
2 2 3  
2 3 3  
1 1 3  
2 3 1  
1 5 5

**Sample Output**

3

如果自己手写逻辑也可以（就像上面的帮派），就是需要写27个逻辑= =

两种思路：

1. [带权值](https://blog.csdn.net/niushuai666/article/details/6981689)- [完全合并](https://blog.csdn.net/lisong_jerry/article/details/80029967)

带权的并查集有更加广阔的应用场景，且空间消耗小于完全合并的方案。

**solution**

```
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
using namespace std;
const int N = 50010;
struct node {
    int pre;       //父节点
    int relation;  //与父节点之间的关系
} p[N];
int find(int x) {  //查找根结点
    int temp;
    if (x == p[x].pre) return x;
    temp = p[x].pre;  //路径压缩
    p[x].pre = find(temp);
    p[x].relation = (p[x].relation + p[temp].relation) % 3;  //关系域更新
    return p[x].pre;  //根结点
}
int main() {
    int n, k;
    int sum = 0;  //假话数量
    scanf("%d%d", &n, &k);
    for (int i = 1; i <= n; ++i) {  //初始化
        p[i].pre = i;
        p[i].relation = 0;
    }
    int ope, a, b;
    for (int i = 1; i <= k; ++i) {
        scanf("%d%d%d", &ope, &a, &b);
        if (a > n || b > n || ope == 2 && a == b) sum++;
        else {
            int root1 = find(a);
            int root2 = find(b);
            if (root1 != root2) {  // 合并
                p[root2].pre = root1;
                // 此时 rootx->rooty = rootx->x + x->y + y->rooty
                p[root2].relation = (3 + p[a].relation + (ope - 1) - p[b].relation) % 3;
            } else {
                //验证x->y之间的偏移量是否与题中给出的d-1一致
                if (ope == 1 && p[a].relation != p[b].relation) sum++;
                else if (ope == 2 && (3 - p[a].relation + p[b].relation) % 3 != ope - 1)
                    sum++;
            }
        }
    }
    printf("%d\n", sum);
    return 0;
}
```

这是向量的思维。  
在这里，向量指的是“偏移量”，表示的是子节点与父节点之间的关系。  
0表示与父节点属于同一种族；  
1表示被父节点吃；  
2表示吃父节点。

显然，路径上的0具备传递性。  
下图推导1和2的传递性，共推导了$A_{2}^2=4$种传递情况，验证了路径压缩时，路径直接相加对3取模即可。

![路径压缩草图](/images/eee77ab052830d3b.png "图片标题")

同理，合并时也应用向量思维：rootx->rooty = rootx->x + x->y + y->rooty
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/26127d24333c455cbb547c89abe24d49），发布于 2020-04-27。
