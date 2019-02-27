---
layout: post
title: leetcode-349-intersection-of-two-arrays
categories: [题目] 
description: leetcode-349-intersection-of-two-arrays
keywords: algorithm
---


题目：[https://leetcode.com/problems/intersection-of-two-arrays/description/](https://leetcode.com/problems/intersection-of-two-arrays/description/)  

* * *

Given two arrays, write a function to compute their intersection. **Example:** Given _nums1_ = [1,2,2,1]  _nums2_ = [2,2]  , return [2]. **Note:**

*   Each element in the result must be unique.
*   The result can be in any order.

* * *

\- ---给两个数组，求这两个数组的交集.....python 是不是两三行解决....   1.      遍历其中一个数组，将另一个数组排序，在这个数组中用二分查找。找到相同的数就存入set集合 中。  复杂度o(nlogn) 二分可以用工具类Arrays.binarySearch().

```java
class Solution {
   public int[] intersection(int[] nums1, int[] nums2) {
       Set<Integer> set = new HashSet();
       Arrays.sort(nums2);
       for(Integer num : nums1){
           if(binarySearch(num,nums2)){
               set.add(num);
           }
      }
       int [] res = new int[set.size()];
       int n = 0;
       for(Integer i : set){
           res[n++] = i;
       }
       return res;
    }
   
   boolean binarySearch(int num, int [] nums){
       int low = 0;
       int high = nums.length - 1;
       while(low <= high){
           int mid = (low+high)/2;
           if(num == nums[mid]){
               return true;
           }
           if(num < nums[mid]){
               high = mid-1;
           }else{
               low = mid+1;
           }
       }
       return false;
   }
}
```

2\.     discuss中有个o(n) 的做法：先将一个数组保存到HashSet中，遍历另一个数组，用set.contains() 判断某个数字是否存在第一个数组中。 (hashSet 是直接计算hash值，判断是否存在比较快，几乎o(1)) ...存在就保存到结果set集合中去重后转移到结果数组内.

```java
public class Solution {
   public int[] intersection(int[] nums1, int[] nums2) {
       Set<Integer> set = new HashSet<>();
      Set<Integer> intersect = new HashSet<>();
       for (int i = 0; i < nums1.length; i++) {
           set.add(nums1[i]);
       }
       for (int i = 0; i < nums2.length; i++) {
           if (set.contains(nums2[i])) {
               intersect.add(nums2[i]);
           }
       }
       int[] result = new int[intersect.size()];
       int i = 0;
       for (Integer num : intersect) {
           result[i++] = num;
       }
       return result;
   }
}
```
