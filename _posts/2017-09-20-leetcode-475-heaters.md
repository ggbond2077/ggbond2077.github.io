---
layout: post
title: leetcode-475-heaters
categories: [技术] 
description: leetcode-475-heaters
keywords: algorithm
---


**题目链接**：[https://leetcode.com/problems/heaters/description/](https://leetcode.com/problems/heaters/description/)  

* * *

Winter is coming! Your first job during the contest is to design a standard heater with fixed warm radius to warm all the houses. Now, you are given positions of houses and heaters on a horizontal line, find out minimum radius of heaters so that all houses could be covered by those heaters. So, your input will be the positions of houses and heaters seperately, and your expected output will be the minimum radius standard of heaters. **Note:**

1.  Numbers of houses and heaters you are given are non-negative and will not exceed 25000.
2.  Positions of houses and heaters you are given are non-negative and will not exceed 10^9.
3.  As long as a house is in the heaters' warm radius range, it can be warmed.
4.  All the heaters follow your radius standard and the warm radius will the same.

**Example 1:**

**Input:** \[1,2,3\],\[2\]
**Output:** 1
**Explanation:** The only heater was placed in the position 2, and if we use the radius 1 standard, then all the houses can be warmed.

**Example 2:**

**Input:** \[1,2,3,4\],\[1,4\]
**Output:** 1
**Explanation:** The two heater was placed in the position 1 and 4. We need to use radius 1 standard, then

 

* * *

    **题目大意：**有许多房子和暖炉在一条水平线上， 给两个整型数组，houses 和 heaters 。 houses 代表房子坐标点 。 heaters代表暖炉的坐标点。所有的暖炉能够温暖的左右范围都一样 称之为暖炉的半径。 现在求使所有的房子都能取到暖的最短暖炉半径。 要求最短的暖炉半径，将所有的房子遍历一遍，求每个房子的左边和右边距离最近的暖炉的距离。 在所有这些最小距离中最大的值就是所求的最短半径。   最小最大问题二分搜索。 我好菜。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。看了别人的思路， 还调了好久。。。     代码用的Arrays工具类提供的sort方法先将暖炉数组排序。再使用其提供的binarySearch方法进行二分查找。。。我以为跟自己写的二分。。一样，然后错了好几发。。。  去看了看源码发现其返回值规则是这样的：   如果查找到则返回对应位置的数组下标(从0开始)。  如果没有查找到返回-(low+1)  ，即该数在数组中应该插入的位置的下标加1取反。(应该插入的位置是指此数在数组中第一次出现右边的数大于等于它的位置) 如 在 数组{2} 中查找 1，2，3 分别返回 -1，0，-2 理解了这个就明白代码中 当index 小于0 时 的处理。始终使index为所查找数字在数组中第一次小于其后面的数的位置  

```java
    class Solution {
       public int findRadius(int[] houses, int[] heaters) {
           Arrays.sort(heaters);
           int res = 0;
           for(int house : houses){
               int index = Arrays.binarySearch(heaters,house);
               if(index < 0)
                   index = -(index+1);
                   int left = index > 0 ? house - heaters[index-1] : Integer.MAX_VALUE;
                   //房子距左边暖炉的距离 当index小于1的时候房子的左边/没有暖炉 距离取最大
                  int right = index < heaters.length ? heaters[index] - house : Integer.MAX_VALUE ;
                  //当index大于等于length时。房子右边没有暖炉 距离取最大
              
               int r = Math.min(right,left);
               res = Math.max(res,r);//取所有最小值的最大值
           }
           return res;
       }
    }
```
