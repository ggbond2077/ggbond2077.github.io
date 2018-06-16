---
layout: post
title: hihocoder-hiho-week-165
categories: [题目] 
description: hihocoder-hiho-week-165
keywords: algorithm
---


题目：[  https://hihocoder.com/contest/hiho165/problem/1](https://hihocoder.com/contest/hiho165/problem/1)

* * *

 

Time Limit:10000ms

Case Time Limit:1000ms

Memory Limit:256MB

### Description

Given a string which contains only lowercase letters 'a'-'z', your task is to reorder the characters so that no two same characters are connected. If there are multiple valid answers output the lexicographically smallest one. If there are no valid answers output INVAlID.

### Input

The string S. (1 ≤ |S| ≤ 100000)

### Output

Output the lexicographically smallest answer or INVALID。

Sample Input

aaabc

Sample Output

abaca

 

* * *

. . . 给一个字符串，调整字符串的顺序，使字符串中不存在两个相同的字符相邻。  如果有多种组合，输出字典序最小的一个。如果不能是的任意两个相邻字符不相同，输出INVALID。 为什么大佬们都说很简单。。。我看了题解做的：  先考虑INVALID的情况。  只有当某个字符的个数大于了(n+1)/2 .才会达不到要求。 字符个数为偶数时 相同字符个数最多为n/2.   aabb  --->   abab    只能间隔插入 字符个数为奇数时  最多相同为n/2 + 1          aaabb  --->  ababa 如果某个字符个数大于了(n+1)/2  一定无法使任意两个相邻字符不同。 题目要求按字典序最小的结果输出。  我们从a-z遍历，取最小满足情况的字符做第一个字符 ， 要使剩下的字符依旧能组成一个合理的子串。即满足(n+1)/2  <= 剩下字符的长度   (剩下的字符合理的组合首个字符一定不会和第一个字符相同，如果相同前面的字符就是INVALID ) 按照规则依次往后取，这样生成的结果一定是最小的。 注意最后两个字符的时候 如：aaabc   前面选出的字符是  aba  剩下ca。  此时子串，ca,ac都是合理组合，但是ac与前面a冲突。 只能选ca。只有在结束的时候会出现这种冲突。 因为奇数会多出的一个字符，必须放在结尾。 我尽力了 尝试自己写题解  ...看不懂就看别人写的吧  ...       [https://hihocoder.com/discuss/question/4714](https://hihocoder.com/discuss/question/4714) code：

```cpp
#include <iostream>
#include <cstring>
using namespace std;

char s[100005];
int dic[26];

int find_max(int n)
{
   int m = 0;
   for(int i = 0; i < 26; i++)
   {
       if(m < dic[i])
      {
           m = dic[i];
       }
   }
   return m;
}

int main()
{
   cin >> s;
   memset(dic,0,sizeof(dic));
   int l = 0;
   while(s[l])
   {
       int v = s[l] - 'a';
       dic[v]++;
       l++;
   }
  int n = l;
   int k = 0;
   char lastChar = ' ';
   int m = find_max(n);

   while(k < l)
   {
       if(m > (n+1)/2)
           break;
       int i;
       for(i = 0; i < 26; i++)
       {
           char c = i+'a';
           if(dic[i] != 0){
               dic[i]--;
               n--;
               if(lastChar != c && (m=find_max(n)) <= (n+1)/2)
               {
                   s[k++] = c;
                   lastChar = c;
                   break;
               }
               else
               {
                   n++;
                   dic[i]++;
               }
           }
       }

   }
   if(k >= l)
   {
       cout << s;
   }
   else
       cout << "INVALID";
   return 0;
}
```

.
