---
layout: post
title: leetcode-436-find-right-interval
categories: [题目] 
description: leetcode-436-find-right-interval
keywords: algorithm,二分
---


TreeMap的简单应用，寻找最小的不大于某个值的数.   题目链接：[https://leetcode.com/problems/find-right-interval/description/](https://leetcode.com/problems/find-right-interval/description/)  

* * *

给一组区间，对于每个区间的结束点：在所有区间的开始点中 找出最小的 大于等于该结束点的值 。输出找到点所属区间下标，不存在为-1\. 例： 输入：  \[1,4\], \[2,3\], \[3,4\]  输出： -1，2，-1 输入：  \[3,4\], \[2,3\], \[1,2\]   输出 ： -1 ，0，-1 输入：  \[1,2\]                         输出： -1     #二分搜索：   用一个list保存所有的区间开始点并排序，用map保存开始点starts对应的下标信息。  遍历区间结束点，在list中二分寻找对应的start值。 再用map得到start值对应的下标  。  

```java
    public int[] findRightInterval(Interval[] intervals) {
       Map<Integer, Integer> map = new HashMap<>();
       List<Integer> starts = new ArrayList<>();
       for (int i = 0; i < intervals.length; i++) {
           map.put(intervals[i].start, i);
           starts.add(intervals[i].start);
      }
       
       Collections.sort(starts);
       int[] res = new int[intervals.length];
       for (int i = 0; i < intervals.length; i++) {
           int end = intervals[i].end;
           int start = binarySearch(starts, end);
           if (start < end) {
               res[i] = -1;
           } else {
               res[i] = map.get(start);
           }
       }
       return res;
    }
    
    public int binarySearch(List<Integer> list, int x) {
       int left = 0, right = list.size() - 1;
       while (left < right) {
           int mid = left + (right - left) / 2;
           if (list.get(mid) < x) { 
               left = mid + 1;
           } else {
               right = mid;
           }
       }
       return list.get(left);
    }
	
```
    TreeMap中有一个ceilingEntry(key)  方法。可以返回最小的 键大于等于key的结点。不存在返回null.   故直接将所有区间 start为键下标i为值保存到map中.   直接遍历所有区间end 调用ceilingEntry（end) 就可以得到结果。 0(nlogn)。    对java中集合还不够熟悉。 不能随心所欲的使用。。。集合源码阅读日后也需要继续补上。 c++ 中对应有lower_bound()方法。  

```java
    /**
     * Definition for an interval.
    * public class Interval {
    *     int start;
    *     int end;
    *     Interval() { start = 0; end = 0; }
    *     Interval(int s, int e) { start = s; end = e; }
    * }
    */
    class Solution {
       public int[] findRightInterval(Interval[] intervals) {
           int n = intervals.length;
           int [] result = new int [n];
           java.util.NavigableMap<Integer,Integer> intervalMap = new TreeMap<>();
           for(int i = 0; i < n; i++){
               intervalMap.put(intervals[i].start,i);
           }
           for(int i = 0; i < n; i++){
               Map.Entry<Integer,Integer> e = intervalMap.ceilingEntry(intervals[i].end);
               result[i] = e == null ? -1 : e.getValue();
           }
           return result;
       }
    }
```	
