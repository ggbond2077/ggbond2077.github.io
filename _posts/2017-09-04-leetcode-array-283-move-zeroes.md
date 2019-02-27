---
layout: post
title: leetcode-array-283-move-zeroes
categories: [题目] 
description: leetcode-array-283-move-zeroes
keywords: algorithm
---


题目：  [ https://leetcode.com/problems/move-zeroes/description/](https://leetcode.com/problems/move-zeroes/description/)

* * *

 

Given an array nums, write a function to move all 0's to the end of it while maintaining the relative order of the non-zero elements.

For example, given nums = [0, 1, 0, 3, 12], after calling your function, nums should be [1, 3, 12, 0, 0].

**Note**:

1.  You must do this **in-place** without making a copy of the array.
2.  Minimize the total number of operations.

**Credits:** Special thanks to [@jianchao.li.fighter](https://leetcode.com/discuss/user/jianchao.li.fighter) for adding this problem and creating all test cases.

* * *

. 给一个数组，将其中所有的0移动到后面，保持原来的非零数字相对位置不变。   要求不能使用额外的空间。 如：[0, 1, 0, 3, 12] 移动后为： [1, 3, 12, 0, 0] 。。。又简单有快速...直接遍历数组，将非零数字从数组头重新填充一遍，最后将后面剩下的置零... 0(n) ----- 我一开始想的是遍历数组，将零与后面任意非零交换位置。。。这样要慢一点。。。可能是0(nlogn)

```java

public void moveZeroes(int[] nums) {
   int i = 0;
   for (int num: nums) {
       if (num != 0) {
            nums[i++] = num;
        }
   }
   while (i < nums.length) {
       nums[i++] = 0;
   }
}
```
