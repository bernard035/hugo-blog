---
title: "Test Taking"
date: 2019-12-21T12:00:00+08:00
draft: false
math: true
showToc: true
categories: ["算法竞赛"]
tags: ["题解"]
---
这是牛客假日团队赛27的D题,也是洛谷P2988。  
当时我们在理解题意上就吃了不小的亏。

题目描述  
Farmer John has to take his annual farming license test. The test comprises N (1 <= N <= 1,000,000) true/false questions. After FJ's dismal performance on last year's test Bessie wishes to help him.  
Bessie has inside information that the number of questions whose answer is true will be one of t1, t2, t3...or tk (0<=ti<= N; 0 <= K <= 10,000) -- even though Bessie has no information about any answer to any specific question. Bessie wants to know the best score that FJ is guaranteed achieve by exploiting this information carefully, even if he doesn't know the actual answers to any test questions.  
To demonstrate Bessie's idea, consider a license test with N=6 questions where Bessie knows that the number of questions with the answer 'true' is either 0 or 3. FJ can always get at least 3 answers correct using logic like this: If FJ answers 'false' on every  
question and there are 0 questions with the answer 'true' then he will get all 6 correct. If there are 3 questions with the answer 'true' then he will get 3 answers correct. So as long as he marks every answer as 'false' he is guaranteed to get at least 3 correct without knowing any answer to the test questions.  
On the other hand, consider what happens if FJ chooses an inferior strategy: he guesses that some 3 answers are 'true' and the other 3 are 'false'. If it turns out that NO answers are 'true' then FJ will get 3 answers correct, the ones he guessed were false. If 3 answers are 'true' then FJ could get as few as 0 answers correct. If he answered incorrectly on all 3 of that answers for which he guessed 'true', and the other 3 (for which he guessed 'false') are true, then he gets 0 correct answers. Even though FJ could get 3 correct in this case by guessing 'false' every time, he can not always guarantee even 1 correct by guessing some 3 answers as 'true', so this strategy can not guarantee getting any answer correct. FJ should use the previous paragraph's strategy.  
Given Bessie's inside information, determine the number of answers FJ can always get correct using this information assuming he uses it optimally.  
输入描述:

- Line 1: Two space-separated integers: N and K
- Lines 2..K+1: Line i+1 contains a single integer: t\_it  
  i  
  ​

输出描述:

- Line 1: A single integer, the best score FJ is guaranteed to achieve  
  示例1  
  输入  
  6 2  
  0  
  3  
  输出  
  3

题目翻译：  
Farmer John要参加一年一度的农民资格考试。考试很简单，只有N个 （1≤N≤1，000，000)true/false的判断题。然而FJ去年考试却“杯具”了，Bessie：希望今年能帮帮他。

Bessie得到可靠的内部消息，有可能有T\_1，T\_2，T\_3，...，或T\_K(0≤T\_i≤N；0≤K≤10，000)

道题的答案为ture：，但具体哪道题的答案是什么却不知道。Bessie希望知道在认真研究了这些内部消息后(虽然不能确定任何一道题的具体答案)，一定保证FJ考试时能获得的最高分数是多少?

为了说明Bessie的想法，考虑N=6的一次考试，Bessie知道答案为true的题的数量是0或者3。FJ可以按这样的做题策略来答对至少3题：如果FJ全部答'false'，那么当有0道题的正确答案是'true'，则FJ答对6题；而当有3道题的正确答案是'true'，则FJ答对3题。因此，只要FJ部答'false'，那么至少一定能答对3题，尽管FJ并不知道每道题的确切答案。

另一方面，考虑如果FJ选择了另一种非最优的做题策略：他猜测某3道题为'true'而另3道题为'false'。当所有题目的正确答案是'false'时，那么FJ能答对3道题。而当有3道题的正确答案是'true'时，那么FJ有可能一道题都答不对。这是因为FJ有可能把3道正确答案为'true'的题全猜成'false'!这说明这种做题策略不如前一种优秀。  
给出Bessie获得的内部消息，计算出FJ采用最优做题策略保证能得到的最高分数是多少?

第1行：2个整数N，K  
第2…K+1行：第i+1行包含一个整数ti  
第1行：一个整数，表示FJ一定能获得的最高分数

也就是说，我们将会得到n种可能情况，每种情况给出对应的可能对的题目数量，因为是判断题，TRUE/FALSE可以转化为 1/0 ，而题目数量可以视为一个01的串，我们需要找到在得知这些信息后能够执行的最优策略，并且求出在最优策略下的最坏情况：这就是答案了。

我们接下来需要理解两个01串的重合部分的最差情况，也就是大佬说的引理：

> 对于两个长度为n的01串，A中a个1，B中b个1，任一排列中相同位置元素相同的数量至少为 max(a+b-n,n-a-b)。

1、a+b>na+b>n，即 AA 和 BB 匹配 1。  
我们为了让相同元素尽可能少，考虑把 AA 中的 1 全移到前端，BB 中的 1 全移到后端，变成**线段覆盖问题**，重合部分为 a+b-na+b−n。

2、a+b<na+b<n，即 AA 和 BB 匹配 0.  
同理，我们考虑把 AA 中的 0 全移到前端，BB 中的 0 全移到后端，用同样的方法得出该情况下结果为 n-a-bn−a−b。

3、a+b=na+b=n，显然答案为 0。

然后就是题目了：  
我们先将 t 从小到大排序，设当前要匹配的01串为S，其中有x个1。对于每对ti−1,ti，我们肯定是拿S和ti−1去匹配0，和ti去匹配 1。  
由引理得 Ans=max(n−ti−1−x,ti+x−n)。

当 n−ti−1−x≥ti+x−n 时，得 Ans≥(ti−ti−1)/2  
同理当 n−ti−1−x≥ti+x−n 时，得Ans≥(ti−ti−1)/2  
综上所述，必有 Ans≥(ti−ti−1)/2。

最后特判t1和tm，全选 0 或全选 1 的情况。

```
#include <iostream>
#include <cstdio>
#include <algorithm>
using namespace std;
long long m,n,a[1000005],result=0;
int main() {
    int i;
    scanf("%lld %lld",&m,&n);
    for(i=0; i<n; i++) {
        scanf("%lld",&a[i]);
    }
    sort(a,a+n);
    result=max(a[0],m-a[n-1]);
    for(i=0; i<n; i++)
        result=max(result,(a[i]-a[i-1])/2);
    printf("%lld\n",result);
    return 0;
}
```

这种题是思维题，感觉平时软院周练侧重逻辑，但是牛客的很多学校的比赛——甚至ICPC都可能更侧重思维的考察，我觉得这些可能比格式之类的更值得投入精力。
---

> 本文整理自作者牛客博客（https://blog.nowcoder.net/n/09f5b3bbbaff48319626e411243c7093），发布于 2019-12-21。
