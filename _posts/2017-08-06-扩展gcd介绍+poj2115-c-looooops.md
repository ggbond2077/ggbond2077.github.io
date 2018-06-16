---
layout: post
title: 扩展gcd介绍+poj2115-c-looooops
categories: [技术] 
description: 扩展gcd介绍+poj2115-c-looooops
keywords: algorithm
---


扩展GCD： **裴蜀定理：**  对于整数a，b. 他们的最大公约数为gcd(a,b) = d  则一定存在x，y 满足 ax + by =d .  同时有ax + by =c存在整数解当且仅当d `|` c.  有无数组x，y解. 扩展gcd就是求其中一对解x，y和最大公约数d. 如何求解？先看推理： a,b为整数 则有a = bt + c.其中 d `|` a , d `|` b故d `|`c, 所以d也为b，c的最大公约数gcd(b,c) =d.  (可以看见辗转相除法的影子： gcd(a,b) = gcd(b,a%b) ). gcd(b,c) = d由裴蜀定理得到 b x1 + c y1 = d.  ····1 由a=bt+c 和 ax+by=d 得 (bt+c)x+by=d 整理得 b(tx+y)+cx =d. ····2 由1,2式可以得到 tx+y = x1 , x=y1.   ===> x = y1 , y = x1-tx 如此得到求解x，y的递推公式. 辗转相除到最后有gcd(d,0) = d , dx+0=d 令 x = 1,y = 0. 回代可求得初始解. 求解扩展gcd递归代码
```cpp

    int ex_gcd(int a,int b,int &x,int &y)
    {
        if(b == 0) 
        {
            x = 1;
            y = 0;
            return a;
        }
        int d = ex_gcd(b,a%b,x,y);
        int xt = x;
        x = y;
        y = xt - (a/b)*y;
    
        return d;
    }
    
```

  以上方法求得的x，y只是解系中的一组解. 很多问题需要求最小正数解 如果求得一组解x0,y0由线性代数知识可以得到基础通解:x = x0+bt , y =y0-at (代回方程始终成立) 可见最小正数解 x ∈[0,b-1] y∈[0,a-1]最小解可以通过模a,b得到 ...线性代数解这个这个我忘了 这里换一种想法得最小正数解： 
  
  考虑一般的线性方程 ax+by=c 可以写成模线性方程 ax≡ c (mod b )  { gcd(a,b) = d.  d `|` a, d `|` b, d `|` c }.方程两边同时除以d可得 (a/d)x + (b/d)y = (c/d) 和 (a/d)x = (c/d) (mod b/d) 这时gcd(a/d,b/d) = 1.得方程 (a/d)x + (b/d) y = 1满足裴蜀定理，用扩展gcd求得解x0,y0. 方程两边同时乘c/d 得到方程(a/d)x + (b/d)y=(c/d) 的解x=x0(c/d) , y =y0(c/d). 这是线性方程的一组解. 由其另一种形式(a/d)x = (c/d) (mod b/d) 可知x加上与减去t倍(b/d) 该等式依然成立 故 通解为x=x0(c/d) + t(b/d).  即x % (b/d) = x0(c/d) ， x的取值范围是[0,b/d-1]. 
  
  根据通解公式可以得到： 当x0为负值时 最小正数解为： x = (x0(c/d)) % (b/d) + (b/d). 当x0为正值时 最小正数解为： x = (x0(c/d)) % (b/d) 

  总结起来就是 ax+by =cb {gcd(a,b)=d , d`|` a , d`|` b} 当且仅当 d`|` c 时方程有解 且最小正数解为 x = (x0*(c/d)) % (b/d) + (b/d).  其中x0 是方程(a/d)x + (b/d)y = 1. 的x解. 现在道理都懂了，怎么做题 以下题为例： poj2115 题目链接:  [http://poj.org/problem?id=2115](http://poj.org/problem?id=2115)

题目

* * *

C Looooops

**Time Limit:** 1000MS

**Memory Limit:** 65536K

**Total Submissions:** 28195

**Accepted:** 8057

Description

A Compiler Mystery: We are given a C-language style for loop of type

for (variable = A; variable != B; variable += C)

  statement;

I.e., a loop which starts by setting variable to value A and while variable is not equal to B, repeats statement followed by increasing the variable by C. We want to know how many times does the statement get executed for particular values of A, B and C, assuming that all arithmetics is calculated in a k-bit unsigned integer type (with values 0 <= x < 2k) modulo 2k.

Input

The input consists of several instances. Each instance is described by a single line with four integers A, B, C, k separated by a single space. The integer k (1 <= k <= 32) is the number of bits of the control variable of the loop and A, B, C (0 <= A, B, C < 2k) are the parameters of the loop. The input is finished by a line containing four zeros.

Output

The output consists of several lines corresponding to the instances on the input. The i-th line contains either the number of executions of the statement in the i-th instance (a single integer number) or the word FOREVER if the loop does not terminate.

Sample Input

3 3 2 16
3 7 2 16
7 3 2 16
3 4 2 16
0 0 0 0

Sample Output

0
2
32766
FOREVER

* * *

题目大意： 给四个数 a,b,c,k. 计算for(i = a; i != b ; i += c) { 这是一条语句;} 的执行次数。 其中k表示a,b,c,d 为k位无符号整数 1<=k<=32. 0 <= a,b,c <= 2^k. 意思就是i加到2的k次方时又会从零开始计数. 如此循环直到等于b退出循环，如果是死循环就输出FOREVER. 输入有多组数据，输入到0 0 0 0 表示结束. 根据题意可以得到模线性方程 a + xc = b (mod 2^k)  化成 ==> cx + (2^k)y = b-a. 这就是上面的线性方程一般式， 有解当gcd(c,2^k) `|` (b-a)  套用方法求解出最小正数解x. 否则无解输出FOREVER. 注意到32位需要用long long , 求2^k 移位运算要写成1LL << 2^k . 输入数据可能有a=b=c=0. 

ac代码：

```cpp

#include <iostream>
using namespace std;
#define LL long long
LL int x,y;

LL exgcd(LL a,LL b)
{
    if(b == 0) 
    {
        x = 1;
        y = 0;
        return a;
    }
    LL d = ex_gcd(b,a%b);
    LL t = x;
    x = y;
    y = t - a/b*y;

    return d;
}

int main()
{
    LL  a,b,c,k;

    while(cin >> a >> b >> c >> k)
   {
       if(a == 0 && b == 0 && c == 0 && k == 0)
           break;
       LL n = 1LL<<k;
       LL d = ex_gcd(c,n);
       LL s = b-a;
       if(s%d != 0)
           cout << "FOREVER" << endl;
       else
       {
           x = x*(s/d) % n;
           x = (x%(n/d) + n/d) % (n/d);
           cout << x << endl;
       }
   }
   return 0;
}

```
