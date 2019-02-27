---
layout: post
title: leetcode-10-Regular Expression Matching模拟正则匹配
categories: [题目]
description: Regular Expression Matching模拟正则匹配
keywords: algorithm
---

模拟正则表达式中的 . 和 \* 号匹配字符串    :https://leetcode.com/problems/regular-expression-matching  

* * *

    Implement regular expression matching with support for '.' and '*'.
    '.' Matches any single character.
    '*' Matches zero or more of the preceding element.
    
    The matching should cover the entire input string (not partial).
    
    The function prototype should be:
    bool isMatch(const char *s, const char *p)
    
    Some examples:
    isMatch("aa","a") → false
    isMatch("aa","aa") → true
    isMatch("aaa","aa") → false
    isMatch("aa", "a*") → true
    isMatch("aa", ".*") → true
    isMatch("ab", ".*") → true
    isMatch("aab", "c*a*b") → true
    

* * *

 

*   实现正则表达式中的.和*号匹配规则。   给两个字符串一个表示待匹配串s 如： aab  另一个表示正则表达式 p 如： c\*a\*b*     判断字符串与表示式是否匹配， 匹配返回true 否则返回false

 

*   暴力搜索：

设i = j = 0.

*   如果s\[i\] == p\[j\] 或者 p\[j\] == '.'  ：   判断如果下一个字符是\*号 则可以匹配零个或多个字符。 匹配多个：递归比较i = i+1 .     匹配零个：跳过当前正则字符和\*号 j=j+2

不是*号可以直接匹配成功，i++,j++ .继续比较下一个字符。

*   如果不匹配并且下一个字符是*号，则可以跳过这个字符。j = j+2 继续比较 ，
*   否则匹配失败。

------------------------------------------------可以倒着比较，遇到\*号就不用考虑越界（\*号前面一定有字符。道理是一样的：

```java
    class Solution {
       public boolean isMatch(String s, String p) {
           int i = s.length();
           int j = p.length();
           return help(s,i-1,p,j-1);
       }
       
       public boolean help(String s,int i,String p, int j){
    
           if(j < 0){
               if(i < 0)
                   return true;
               else
                   return false;
           }
    
           if(p.charAt(j) == '*'){//为* j一定大于0
               if(i >= 0 &&(p.charAt(j-1) == s.charAt(i) || p.charAt(j-1)=='.')){
                   if(help(s,i-1,p,j))
                       return true;
               }
               return help(s,i,p,j-2);
           }
    
           if(i >= 0 && (s.charAt(i) == p.charAt(j) || p.charAt(j)=='.')){
               return help(s,i-1,p,j-1);
           }
           return false;
       }
    }
    
```


动态规划：

使用二维数组dp\[\]\[\]保存信息，  dp\[i\]\[j\]表示 s\[0\]-s\[i\] 与 p\[0\]-p\[j\] 是否匹配. true ,false 表示.
- 如果p.charAt(j) == s.charAt(i) 或  p.charAt(j) == '.'  则 dp\[i\]\[j\] 等于dp\[i-1\]\[j-1\] 的匹配状态。
- 如果p.charAt(j) == '\*'  : 
  1. p.charAt(j-1) != s.charAt(i) \*号前面的字符与s\[i\] 不相同，此时应跳过这个 字符和\* 也就是\*表示零个字符。dp\[i\]\[j\] = dp\[i\]\[j-2\] 
  2. p.charAt(j-1) == s.charAt(i) or p.charAt(j-1) == '.' 此时\*号可以表示零个或多个字符。 表示零个, 跳过\*号：  dp\[i\]\[j\] = dp\[i\]\[j-2\] 表示1个 s\[i\] 和p\[j\] 的前一个匹配： dp\[i\]\[j\] = dp\[i\]\[j-1\] ,表示多个 j继续指向*号匹配下一个：dp\[i\]\[j\] = dp\[i-1\]\[j\] 
  
  代码来自disscuss:

```java
       public boolean isMatch(String s, String p) {
    
           if (s == null || p == null) {
               return false;
           }
           boolean[][] dp = new boolean[s.length()+1][p.length()+1];
           dp[0][0] = true;
           for (int i = 0; i < p.length(); i++) {
               if (p.charAt(i) == '*' && dp[0][i-1]) {
                   dp[0][i+1] = true;
               }
           }
           for (int i = 0 ; i < s.length(); i++) {
               for (int j = 0; j < p.length(); j++) {
                   if (p.charAt(j) == '.') {
                       dp[i+1][j+1] = dp[i][j];
                   }
                   if (p.charAt(j) == s.charAt(i)) {
                       dp[i+1][j+1] = dp[i][j];
                   }
                   if (p.charAt(j) == '*') {
                       if (p.charAt(j-1) != s.charAt(i) && p.charAt(j-1) != '.') {
                           dp[i+1][j+1] = dp[i+1][j-1];
                       } else {
                           dp[i+1][j+1] = (dp[i+1][j] || dp[i][j+1] || dp[i+1][j-1]);
                       }
                   }
    
               }
           }
           return dp[s.length()][p.length()];
       }
```
