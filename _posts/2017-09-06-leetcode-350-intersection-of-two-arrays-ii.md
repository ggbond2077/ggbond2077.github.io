---
layout: post
title: leetcode-350-intersection-of-two-arrays-ii
categories: [题目] 
description: leetcode-350-intersection-of-two-arrays-ii
keywords: alogrithm
---


每天做了两个题，挑一个稍微有意思点的来记录 题目：[https://leetcode.com/problems/intersection-of-two-arrays-ii/description/](https://leetcode.com/problems/intersection-of-two-arrays-ii/description/)

* * *

Given two arrays, write a function to compute their intersection. **Example:** Given _nums1_ = [1,2,2,1]   _nums2_ = [2,2], return [2,2]. **Note:**

*   Each element in the result should appear as many times as it shows in both arrays.
*   The result can be in any order.

**Follow up:**

*   What if the given array is already sorted? How would you optimize your algorithm?
*   What if _nums1_'s size is small compared to _nums2_'s size? Which algorithm is better? What if elements of _nums2_ are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?
*   * * *
    
    \- \- \- \- 这是上一个找两个数组的并集的升级版，之前重复的元素不算，所以将数组放到set集合中可以去重。  今天这个要求重复的也记录 1. 还是差不多的做法，先将两个数组排序，然后一遍循环，设立两个标记，同时遍历两个数组，相等就记录到结果数组。 否则按情况移动标记。

```java
    class Solution {
       public int[] intersect(int[] nums1, int[] nums2) {
           Arrays.sort(nums1);
           Arrays.sort(nums2);
           int l1 = nums1.length;
           int l2 = nums2.length;
           
           
           List<Integer> list = new ArrayList<Integer>();
           int i = 0, j = 0;
           while(i < l1 && j < l2){
                if(nums1[i] < nums2[j]){
                    i++;
                }else if(nums1[i] > nums2[j]){
                    j++;
                }else{
                    list.add(nums1[i]);
                    i++;j++;
                }
           }
           
          int[] res = new int [list.size()];
           int n  =0;
           for(int num : list){
               res[n++] = num;
           }
           return res;
       }
    }

```
    
  2.  还有一个用map来做的版本，我第一版是这样的。没调出来。。。。感觉java map没有c++ 好用。。。 用一个map记录其中的一个数组，以数组的值为键，每一个值出现的个数为值。。。在遍历另一个数组时，出现一个map.containKey()为true的就记录到结果，map的值减一
    
```java
    public class Solution {
       public int[] intersect(int[] nums1, int[] nums2) {
           HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
           ArrayList<Integer> result = new ArrayList<Integer>();
           for(int i = 0; i < nums1.length; i++)
          {
               if(map.containsKey(nums1[i])) map.put(nums1[i], map.get(nums1[i])+1);
               else map.put(nums1[i], 1);
           }
       
          for(int i = 0; i < nums2.length; i++)
           {
               if(map.containsKey(nums2[i]) && map.get(nums2[i]) > 0)
               {
                   result.add(nums2[i]);
                   map.put(nums2[i], map.get(nums2[i])-1);
               }
           }
      
          int[] r = new int[result.size()];
          for(int i = 0; i < result.size(); i++)
          {
              r[i] = result.get(i);
          }
       
         return r;
       }
    }
    
```

    
  这个题目还有三个扩展问题：
   *   What if the given array is already sorted? How would you optimize your algorithm?
   *   What if _nums1_'s size is small compared to _nums2_'s size? Which algorithm is better?
   *   What if elements of _nums2_ are stored on disk, and the memory is limited such that you cannot load all elements into tthe memory at once? 1.如果给的数组已经排好序，用什么方法？   第一种方法，遍历一遍数组o(n) 2.如果nums1的大小比nums2小 ：     就用第二种方法，将小的nums1 保存到map中。 3. 如果两个数组在磁盘上，不能一次性读入内存 ：  用外部排序将两个数组排好序，使用第一种方法，遍历比较，在文件中移动文件指针。
