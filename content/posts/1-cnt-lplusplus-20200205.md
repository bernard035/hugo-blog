---
title: "寒假训练赛1 双指针 --cnt[s[l++]]"
date: 2020-02-05T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛", "算法竞赛/赛后分析"]
tags: ["赛后分析", "牛客"]
---
每次打比赛都能有一些收获，这次主要有以下几个点：  
最早在看《算法竞赛入门到进阶》的时候看到了《代码规范》，我觉得很多都很好，因为这样规范不只可以让代码更统一，很多的细节的地方更可以避免不必要的问题，但是有一个点我当时不是很明白，就是“变量定义”，他建议变量在离使用最近的地方定义，我主要有两个点比较疑惑：1.把一些变量和数组定义成全局变量可以少写很多函数参数，方便很多；2.游标值有的时候可以再利用。  
这次打比赛就出现了这个问题，我把游标变量i定义成的全局变量，导致我看了很久都没发现居然是这个问题错了……总之就是代码规范是长期实践总结出来的经验，**按照代码规范来写可以有效地避免没有想到的bug**。

然后这次比赛的A题是用python过的，需要再花时间掌握分步取模（也就是CZL说的拆解）。

寒假的前半段时间主要花在了啃一些硬骨头上面，可能确实比他们慢一些，但是这样至少扎实一些，我觉得是值得的。所以我其实没有掌握太多，目前做位运算的练习稍多一些，本来想在比赛前看看并查集的，但是完全来不及了，临时补了一下矩阵快速幂也没用上。（此处感谢青竹大佬的帮助

另外这次比赛一个明显的问题就是cin/cout不敢用 因为慢。而且我G题本来TLE，加了这句话

```
ios_base::sync_with_stdio(false);cin.tie(0);cout.tie(0);
```

以后直接RE了，不知道为什么

## G题

<https://ac.nowcoder.com/acm/contest/3002/G>  
其实算是我这次比赛的遗憾了，过的人数比我的排名高……  
我当时的实现想法就是一个从k到n逐渐变大的滑块，不断检索。O(N^2)，但是实际上会小一些，差不多是O(N^1.5)?  
估计ZT他们也是差不多的思路，反正就是TLE了。  
其实打字打到这里我就有了一些更多的想法，比如 检索到了就直接return 不应该单独逐次统计，这样的话每一个二级循环对应的是大约30个语句，如果实现了这个优化可能可以让二级循环每个对应的语句减少80%。  
啊……我比赛的时候怎么没想到。  
其实和下面的思路在实现上是差不多的：就是一边进元素一边查。

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int n,k;
string str;
map<char,int> mp;
int main(){
    cin>>n>>k>>str;
    int ans=-1,l=0,cnt=0;
    for(int i=0;i<n;i++){
        char c=str[i];
        mp[c]++;
        while(mp[c]==k){
            if(ans==-1) ans=i-l+1;
            else ans=min(ans,i-l+1);
            mp[str[l]]--;
            l++;
        }
    }
    printf("%d\n", ans);
    return 0;
}
```

其实根本没用到map，时间上也不是很优秀——但就是这样本菜鸡都没想到。  
它的实现逻辑就是从前往后找，找到了以后再删前面的，最后头尾减一下就是了。

```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn=2e5+10;
char s[maxn];
vector<int> g[26];//26个容器
int main() {
    int n,k;
    cin>>n>>k>>s;
    for(int i=0; i<n; i++)
        g[s[i]-'a'].push_back(i);//统计整个字串的字母及其出现的位置
    int ans=n+1;
    for(int i=0; i<26; i++)
        if(g[i].size()>=k) 
            for(int j=k-1; j<g[i].size(); j++)
                ans=min(ans,g[i][j]-g[i][j-k+1]+1);//比最小
    if(ans!=n+1) cout<<ans<<endl;
    else puts("-1\n");
}
```

这个容器的写法也很漂亮。复杂度是O(n)，语句数量差不多是4n左右。

```
#include <bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 7;
typedef long long ll;
char s[maxn];
int n, k, p[30];
bool check(int x) {
    memset(p, 0, sizeof(p));
    for (int i = 1; i <= x; i++) {//这个是从原始字串的左端开始 长度为x 
        p[s[i] - 'a']++;
        if(p[s[i] - 'a'] >= k) return true;//边进边查 查到返回 
    } 
    for (int i = x + 1; i <= n; i++) {//第二个指针
    //上次没有搜到的部分 如果程序走到了这里，就说明头x个字母不满足 
        p[s[i] - 'a']++;//选取
        p[s[i-x] - 'a']--;//丢弃 
        if(p[s[i] - 'a'] >= k) return true;
    }
    return false;
}
int main() {
    cin >> n >> k;
    scanf("%s",s +1);
    int left = 1, right = n, ans = -1;
    while (left <= right) {
        int mid = left+((right-left)>>1);//这种写法比青竹大佬的写法更好
        if(check(mid)) {
            ans = mid;
            right = mid - 1;
        } else left = mid + 1;
    }
    printf("%d\n", ans);
    return 0;
}
```

青竹大佬的二分写法真的亮到我了。  
我最先看的就是这个写法，首先在外层的长度选取上使用二分，然后在内层的已知长度求是否满足的函数里使用双指针，这样就是一个O(nlogn)的复杂度。  
啊 我为什么没想到。

---

三月回来重做G题，wa了，忽视了多个满足k的情况的比较（也忘记了可以用双指针。

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 2e5 + 5;
int main() {
    int n, k;
    string s;
    cin >> n >> k >> s;
    map<char, int> mp;
    int ans = -1;
    for (int R = 0, L = 0; R < n; ++R) {
        ++mp[s[R]];
        if (mp[s[R]] == k) {
            for (; L < R; L++) {
                if (s[L] == s[R]) break;
                --mp[s[L]];
            }
            if (ans == -1)
                ans = R - L + 1;
            else
                ans = min(ans, R - L + 1);
            --mp[s[L++]];//释放最左侧的位点，才能进行下一轮比较
        }
    }
    cout<<ans<<endl;
    return 0;
}
```

## H题

<https://ac.nowcoder.com/acm/contest/3002/H>

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 2e5 + 5;
int main() {
    int n, k;
    string s;
    cin>>n>>k>>s;
    map<bool,int>cnt;
    int ans=-1;
    for(int i=0,l=0;i<n;++i){
        ++cnt[s[i]-'0'];
        while(min(cnt[0],cnt[1])==k){
            char M;
            if(cnt[0]>cnt[1])M='0';
            else M='1';
            int j=i+1;
            for(;s[j]==M&&j<n;++j) ;
            ans=max(j-l,ans);
            --cnt[s[l++]-'0'];
        }
    }
    if(ans==-1) cout<<n<<endl;
    else cout<<ans<<endl;
    return 0;
}
```

这个完全是我自己想出来的，其实和G很像，wa了两次才想出来要加一个后缀搜索
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/e432e0bfe18a49578239bc6c452ce6c2），发布于 2020-02-05。
