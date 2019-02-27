---
layout: post
title: leetcode-array-561-array-partition-i
categories: [题目] 
description: leetcode-array-561-array-partition-i
keywords: algorithm
---


用java好好刷点题吧。。。从简单到复杂。 **题目：    **[https://leetcode.com/problems/array-partition-i/description/](https://leetcode.com/problems/array-partition-i/description/)

* * *

Given an array of **2n** integers, your task is to group these integers into **n** pairs of integer, say (a1, b1), (a2, b2), ..., (an, bn) which makes sum of min(ai, bi) for all i from 1 to n as large as possible. **Example 1:**

**Input:** \[1,4,3,2\]

**Output:** 4
**Explanation:** n is 2, and the maximum sum of pairs is 4 = min(1, 2) + min(3, 4).

**Note:**

1.  **n** is a positive integer, which is in the range of \[1, 10000\].
2.  All the integers in the array will be in the range of \[-10000, 10000\].
    
    * * *
    
    **题目大意**： 给2n个数字，把他们两个一组，分成n组。 使得每一组的最小值之和最大。   求最大值。 **解：** 每组都是取的最小值，损失了另外一个数，为了使损失最小，另外一个数最好是离取得的数最近的数。即将两个相近的数分为一组。。 排序后，就满足需求。对奇数位的数求和。
    
```java
    public class Solution {
       public int arrayPairSum(int\[\] nums) {
           Arrays.sort(nums);
           int result = 0;
           for (int i = 0; i < nums.length; i += 2) {
               result += nums\[i\];
           }
          return result;
       }
    }
```
