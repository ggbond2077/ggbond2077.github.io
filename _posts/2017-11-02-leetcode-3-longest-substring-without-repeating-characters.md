---
layout: post
title: leetcode-3-longest-substring-without-repeating-characters
categories: [题目] 
description: leetcode-3-longest-substring-without-repeating-characters
keywords: algorithm
---


寻找一个字符串中不含重复元素的最长子串 题目链接： [https://leetcode.com/problems/longest-substring-without-repeating-characters](https://leetcode.com/problems/longest-substring-without-repeating-characters)  

    Given a string, find the length of the longest substring without repeating characters.
    
    Examples:
    
    Given "abcabcbb", the answer is "abc", which the length is 3.
    
    Given "bbbbb", the answer is "b", with the length of 1.
    
    Given "pwwkew", the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subseq
    uence and not a substring.
    
    

  给一个字符串，包含各种字符。寻找最长的不含重复元素的字串。 输出长度 自己意淫的一个很慢的方法： 遍历字符串，map存储当前字符是否在子串中，1为存在，0或null为不存在。 然后一边更新map一边更新最大长度。。。

```java
    class Solution {
       public int lengthOfLongestSubstring(String s) {
           java.util.Map<Character,Integer> map = new java.util.HashMap<Character,Integer>();
           int n = s.length();
           int res = 0;
           int len = 0;
           int start = 0;
           for(int i = 0; i < n; i++){
               char c = s.charAt(i);
               if(map.get(c) == null || map.get(c) == 0){
                   len++;
                   map.put(c,1);
               }else{
                   if(len > res)
                       res = len;
                   int j = s.indexOf(c,start);
                   for(int k = start; k < j; k++){
                       c = s.charAt(k);
                       map.put(c,0);
                       len--;
                  }
                   start = j+1; 
               }
               
           }
           if(len > res)
               res = len;
           return res;
       }
    }
    
```
    

  题解中o(n) 的方法：   同样遍历字符串，用i指向子串末端，j指向子串首端，map用来记录上一次此字符出现的位置， 当遇到重复元素时。j 直接移到上一次出现此字符的后面位置。 我好像也是这么想的。代码表达错了。。写着写着就偏了。。。（开始以为只有26个英文字符。。。 :) '''  同样使用map，别人用得这么优雅。 同样是java 别人写得这么优雅。 受教 pf.jpg

```java
    public int lengthOfLongestSubstring(String s) {
               if (s.length()==0) return 0;
               HashMap<Character, Integer> map = new HashMap<Character, Integer>();
               int max=0;
               for (int i=0, j=0; i<s.length(); ++i){
                   if (map.containsKey(s.charAt(i))){
                       j = Math.max(j,map.get(s.charAt(i))+1);
                  }
                   map.put(s.charAt(i),i);
                   max = Math.max(max,i-j+1);
               }
               return max;
           }
```
