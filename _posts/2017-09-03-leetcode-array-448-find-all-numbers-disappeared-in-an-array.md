---
layout: post
title: leetcode-array-448-find-all-numbers-disappeared-in-an-array
categories: [题目] 
description: leetcode-array-448-find-all-numbers-disappeared-in-an-array
keywords: algorithm
---


**题目：**[https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/description/](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/description/)

* * *

Given an array of integers where 1 ≤ a[i] ≤ _n_ (_n_ = size of array), some elements appear twice and others appear once. Find all the elements of [1, _n_] inclusive that do not appear in this array. Could you do it without extra space and in O(_n_) runtime? You may assume the returned list does not count as extra space. **Example:**

Input:

[4,3,2,7,8,2,3,1]

**Output:**
[5,6]

* * *

**题目大意：** 
  给一个大小为n的数组，其中每个元素都是正整数取值范围为[1,n]，允许重复.  要求找出1-n中没有在数组中出现的数。  要求时间复杂度为o(n) 空间除了给定数组外，最多额外使用一个o[n]的空间 我的解好像有点不美观。  先将1-n存入一个list中，再遍历给定数组，将其中出现的数字在list中标记为空。   接下来将剩下的未被标记为空的数字就是没有出现过的。把他们转移到给定数组内。  清空list。最后再转移到list中.........不好在，改变了原有数据。而且绕过来过去。


```java

class Solution {
   public List<Integer> findDisappearedNumbers(int[] nums) {
       List<Integer> list = new ArrayList<Integer>();
       int n = nums.length;
       
       list.add(null);
       for(int i = 1; i <= n; i++){
           list.add(i);
       }
       for(int i : nums){
           list.set(i,null);
       }
       int j = 0;
       
       for(Integer i : list){
          if(i != null){
               nums[j++] = i;
           }
       }
      list.clear();
       for(int i = 0; i < j; i++){
           list.add(nums[i]);
      }
       return list;
   }
```

再来看看discuss中给的答案： 遍历一遍给定数组，令  nums[nums[i] -1] = -nums[nums[i]-1] ， 巧妙的利用原数组标记出现过的数字。 将原位置的数据取负，表示此下标对应的数字出现过，并且只需求绝对值就能还原出的原来数。 数据的所传达信息没有改变。

```java
   public List<Integer> findDisappearedNumbers(int[] nums) {
       List<Integer> ret = new ArrayList<Integer>();
       
       for(int i = 0; i < nums.length; i++) {
           int val = Math.abs(nums[i]) - 1;
           if(nums[val] > 0) {
               nums[val] = -nums[val];
           }
      }
       
       for(int i = 0; i < nums.length; i++) {
           if(nums[i] > 0) {
               ret.add(i+1);
           }
       }
      return ret;
   }

```
