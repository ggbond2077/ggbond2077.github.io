---
layout: post
title: leetocde-287-find-the-duplicate-number寻找数组中重复元素的多种方法
categories: [题目] 
description: leetocde-287-find-the-duplicate-number寻找数组中重复元素的多种方法
keywords: algorithm
---


找出数组中唯一的一个重复元素。 顺带包括了链表中找环的问题。 题目：[https://leetcode.com/problems/find-the-duplicate-number/description/](https://leetcode.com/problems/find-the-duplicate-number/description/)  

* * *

Given an array _nums_ containing _n_ \+ 1 integers where each integer is between 1 and _n_ (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one. **Note:**

1.  You **must not** modify the array (assume the array is read only).
2.  You must use only constant, _O_(1) extra space.
3.  Your runtime complexity should be less than O(n2).
4.  There is only one duplicate number in the array, but it could be repeated more than once.
    
    * * *
    

题目大意:   一个包含n+1 个元素的整型数组。其中每个元素的取值范围是\[1，n\]  这中间肯定有重复元素。      假设现在只有一个数字重复。   找出这个数字。 要求：  1.不能改变原数组  2.空间复杂度o(1)  3.时间复杂度不超过o(n^2)   4.这一个数字可重复多次   要是不理会一连串要求。这个题目有多种解法：   **解法一**：   采用o(n) 空间复杂度 ： 简单利用map 或者一个额外的辅助数组。遍历一遍便找出重复元素   **解法二**：    不使用额外空间，但改变原数组。 遍历数组。将对应的值交换到对应的下标。(如使 a\[1\] = 1   a\[2\] =2  a\[3\] =3   a\[4\] = 4 ...)   当交换时如果改值与下标已经对应，即重复

```java
            public static int findDuplicate(int[] nums) {
                int n = nums.length;
                for(int i = 0; i < n; i++){
                    while((i+1) != nums[i]){                    
                        if(nums[nums[i]-1] == nums[i]){
                            return nums[i];
                        }else{
                            int t = nums[i];
                            nums[i] = nums[t-1];
                            nums[t-1] = t;
                        }
    
                    }
                }
                return -1;
            }
    
```

  **解法三**： 如果只有一个重复元素且只重复一次的话。 可根据位运算的性质：  一个数a 与同一个数b 异或两次，结果是a    a^b^b=a(:证明略:)   . 。  则重复元素 r =  1^2^3^....^n ^ a\[1\] ^ a\[2\] ^ ... a\[n\].    ： 因为异或运算是满足交换律的，将a\[i\] 的值
放入。 交换一下，根据前面的性质就推断出来了.  

```java
            public static int findDuplicate(int[] nums) {
                int n = nums.length;
                int res = 1;
            for(int i = 2; i <= n; i++){
                    res ^= i;
            }
            for(int i = 0; i < n; i++){
                res ^= nums[i];
            }
                return res;
            }
    
```
            接下来是解此题的正确姿势了。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。 先看看链表中找环的问题： 判断一个链表中是否有环： **141\. Linked List Cycle**     ：[https://leetcode.com/problems/linked-list-cycle/description/](https://leetcode.com/problems/linked-list-cycle/description/) 升级版找出一个链表中开始进入环的位置： **142\. Linked List Cycle II** ：[https://leetcode.com/problems/linked-list-cycle-ii/description/](https://leetcode.com/problems/linked-list-cycle-ii/description/)   关于找环的方法与证明，移步：        [  http://www.cnblogs.com/wuyuegb2312/p/3183214.html](http://www.cnblogs.com/wuyuegb2312/p/3183214.html)     再来看这个问题：    设数组为\[ 2 5 1 1 4 3 \] 下标从1开始 则： 下标：1  2  3  4  5  6 值    ：2  5  1  1  4  3 从下标6开始，将下标对应的值作为新的下标。 形成如下的链表 ![](https://www.ggbond.cc/wp-content/uploads/2017/10/dumplicate.png) (由于数组中存在重复元素，这样一会会形成一个带环的链表。）  但是开始小标选择应该是最后的n+1  因为： 1 2 3 4 5 6 1 2 4 3 5 2 这样的情况如果选1，2， 都无法继续连接。  选择最后一个下标n+1  因为元素最大为n ， n+1 对应的值一定不是n+1.   实际的下标是从0开始的，代码中做处理：  

```java
       public int findDuplicate(int[] nums) {
           int n = nums.length;
           int slow = n;
           int fast = n;
           
           do{
               slow = nums[slow-1];
               fast = nums[nums[fast-1]-1];
           }while(slow != fast);
           
           slow = n;
           do{
               slow = nums[slow-1];
               fast = nums[fast-1];
           }while(slow != fast);
       
           return slow;
       }
```

  最后。。。 这个题还能用二分做？？？ 二分 1-n.  判断小于等于mid 的个数cnt，(因为数组元素是连续的1-n  小于等于mid的数一定一次为1,2,3..mid, 刚好为mid个)     如果cnt > mid 说明mid前面有重复元素，缩小范围继续搜索。

```java
    public int findDuplicate(int[] nums) {
        int low = 1, high = nums.length - 1;
       while (low <= high) {
           int mid = (int) (low + (high - low) * 0.5);
           int cnt = 0;
           for (int a : nums) {
               if (a <= mid) ++cnt;
           }
           if (cnt <= mid) low = mid + 1;
           else high = mid - 1;
       }
       return low;
    }
```
