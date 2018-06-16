---
layout: post
title: leetcode-454-4sum-ii
categories: [题目] 
description: leetcode-454-4sum-ii
keywords: algorithm
---


[https://leetcode.com/problems/4sum-ii/description/](https://leetcode.com/problems/4sum-ii/description/)  

* * *

Given four lists A, B, C, D of integer values, compute how many tuples (i,j,k,l) there are such that (A\[i\]+B\[j\]+C\[j\]+D\[l\]) is zero. To make problem a bit easier, all A, B, C, D have same length of N where 0 ≤ N ≤ 500. All integers are in the range of -228 to 228 \- 1 and the result is guaranteed to be at most 231 \- 1\. **Example:**

**Input:**
A = \[ 1, 2\]
B = \[-2,-1\]
C = \[-1, 2\]
D = \[ 0, 2\]

**Output:**
2

**Explanation:**
The two tuples are:
1\. (0, 0, 0, 1) -> A\[0\] + B\[0\] + C\[0\] + D\[1\] = 1 + (-2) + (-1) + 2 = 0
2\. (1, 1, 0, 0) -> A\[1\] + B\[1\] + C\[0\] + D\[0\] = 2 + (-1) + (-1) + 0 = 0

* * *

给四个整型数组A.,B,C,D. 找出有多少对(i,j,k,l) 使得 (A\[i\]+B\[j\]+C\[j\]+D\[l\]) = 0.   假设四个数组的长度都一样。 四个数组。。怎么二分 :cry:....  三个加起来，分最后一个 N^3*logN...超时、

看题解：   将两个数组逐项求和。 保存和相同的个数到map中， 对另外两个数组逐项求和，与之前的和互为相反数的 即满足条件四个数组和为零. 从map中取出值累加就为最终结果   还有这种操作？ o(n^2)

```java
    class Solution {
       public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
           int num = 0;
           int n = A.length;
           Map<Integer,Integer> m = new HashMap<Integer,Integer>();
           for(int i = 0; i < n; i++){
               for(int j = 0; j < n; j++){
                   int sum = A[i]+B[j];
                  m.put(sum,m.getOrDefault(sum,0)+1);
               }
           }
           for(int i = 0; i < n; i++){
              for(int j = 0; j < n; j++){
                   int sum = -(C[i] + D[j]);
                   num += m.getOrDefault(sum,0);
               }
           }
           return num;
       }
    }
```
