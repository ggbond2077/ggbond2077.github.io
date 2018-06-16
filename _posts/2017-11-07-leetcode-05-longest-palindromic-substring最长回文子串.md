---
layout: post
title: leetcode-05-longest-palindromic-substring最长回文子串
categories: [题目] 
description: leetcode-05-longest-palindromic-substring最长回文子串
keywords: algorithm,dp
---


求字符串的最长回文子串：[https://leetcode.com/problems/longest-palindromic-substring](https://leetcode.com/problems/longest-palindromic-substring)  

* * *

  **1.**遍历字符串，以当前字符为中心。向两边扩散比对。o(n^2)  .  注意当回文串为奇数偶数情况不一样。 我的臃肿代码：

```java
    class Solution {
       int a = 0,b = 0;
       int len = 0;
       public String longestPalindrome(String s) {
       int n = s.length();
       int j = 0;
       int k = 0;
       for(int i = 0; i < n; i++){
            j = i;
            k = i;
    
            getLen(j,k,n,s);
    
            if(i > 1 && s.charAt(i-1) == s.charAt(i)){
            j = i-1;
            k = i;
            getLen(j,k,n,s);
            }else if(i < n-1 && s.charAt(i+1) == s.charAt(i)){
            j = i;
            k = i+1;
            getLen(j,k,n,s);
            }
    
        }
          
        return s.substring(a,b);
       }
       public void getLen(int j,int k,int n,String s){
       while(j >= 0 && k < n){
            if(s.charAt(j) != s.charAt(k)){
            break;
            }
            j--;
            k++;
        }
        if((k-j-1) > len){
            len = k-j-1;
            a = j+1;
            b = k;
        }
       }
    
    }
    
```

  题解的优雅代码：

```java
       public class Solution {
        private int lo, maxLen;
    
       public String longestPalindrome(String s) {
           int len = s.length();
           if (len < 2)
               return s;
           
           for (int i = 0; i < len-1; i++) {
               extendPalindrome(s, i, i);  //assume odd length, try to extend Palindrome as possible
               extendPalindrome(s, i, i+1); //assume even length.
           }
          return s.substring(lo, lo + maxLen);
       }
    
      private void extendPalindrome(String s, int j, int k) {
            while (j >= 0 && k < s.length() && s.charAt(j) == s.charAt(k)) {
               j--;
               k++;
           }
           if (maxLen < k - j - 1) {
               lo = j + 1;
               maxLen = k - j - 1;
           }
       }
	}
    
```


2、动态规划解法
========

设P\[i,j\] 表示以i开始以j结束的子串，如果p\[i,j\]是回文字符串，那么P\[i+1,j-1\]也是回文字符串。 状态方程和转移方程： P\[i,j\]=false 表示子串\[i,j\]不是回文串。P\[i,j\]=true表示子串\[i,j\]是回文串。 P\[i,i\]=true P\[i,j\]  = P\[i+1,j-1\]  &&  s\[i\]==s\[j\]

```java
            //初始化字典P
           for(int i=0;i<length;i++) 
           {  
               P[i][i]=true;  
               if(i<length-1&&s.charAt(i)==s.charAt(i+1))  
               {  
                   P[i][i+1]=true;  
                   start=i;  
                  maxlength=2;  
               }  
           } 
            //填字典 
           for(int len=3;len<=length;len++)//子串长度  
               for(int i=0;i<=length-len;i++)//子串起始地址  
               {  
                   int j=i+len-1;//子串结束地址  
                   if(P[i+1][j-1]&&s.charAt(i)==s.charAt(j))  
                   {  
                       P[i][j]=true;  
                       maxlength=len;  
                       start=i;  
                   }  
                }
```


1.  网上发现的[-Manacher](http://www.cnblogs.com/Lyush/p/3221503.html)算法：  [ http://www.cnblogs.com/Lyush/p/3221503.html](http://www.cnblogs.com/Lyush/p/3221503.html)
