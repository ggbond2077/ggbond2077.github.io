---
layout: post
title: leetcode-378-kth-smallest-element-in-a-sorted-matrix
categories: [题目] 
description: leetcode-378-kth-smallest-element-in-a-sorted-matrix
keywords: algorithm
---

题目链接：[https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/description/](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/description/)  

    Given a <i>n</i> x <i>n</i> matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.
    
    Note that it is the kth smallest element in the sorted order, not the kth distinct element.
    
    <b>Example:</b>
    <pre>matrix = [
       [ 1,  5,  9],
       [10, 11, 13],
       [12, 13, 15]
    ],
    k = 8,
    
    return 13.
    </pre>
    <b>Note: </b>
    You may assume k is always valid, 1 ≤ k ≤ n<sup>2</sup>.
    
     
    
    

 给一个n*n的二维数组matrix ， 它的所有行和列都是升序排列。求这个矩阵中第k小的数。    

*   第一种办法是二分。  二分有两种搜索方式，一是按索引分。 而是按照数据的范围分。  对一个排好序的一维数组采用第一种方式。否则按照范围分。本题只能按照数据范围分

将所给数据的最小值和最大值作为low 和high 。 搜索判断mid值是否满足第k小的条件。    本题由于行列都有序。判断时可以加快速度。  复杂度o((m+n)*logn)  

```java
    class Solution {
       public int kthSmallest(int[][] matrix, int k) {
          int n = matrix.length;
          int m = matrix[0].length;
           
           int low = matrix[0][0];   //最小值
           int high = matrix[n-1][m-1];  //最大值
           while(low <= high){
               int mid = low + (high-low)/2;
              //判断是否满足条件部分
                int count = 0;
               int j = m-1;
               for(int i = 0; i < n; i++){
                   while(j >= 0 && matrix[i][j] > mid)  //由于列是按升序排列，j可以不必每次从数组尾部开始减
                       j--;
                   count += (j+1);    //count 表示值小于等于mid的个数
              }
               if(count < k)   
                   low = mid+1;
               else    
                   high = mid-1;   // count==k 说明mid是第k大的值，但是mid可能不在原数组中。所以不能直接返回mid。应继续搜索
           }
           return low;
      }
    }
```


*   第二种方法是最小堆。（来自discuss java提供的PriorityQueue集合内部实现为最小堆数据结构。   先用二维数组第一行创建堆。 执行k-1 次的出堆，入堆操作。  每一次入堆只需要插入上一次取出的元素相同列的下一行即可。因为下一个最小值只可能在其下一个元素和当前堆中元素产生。      所以这里需要记录元素的行和列标号。  定义一个Tuple类。               第k次出堆的数就是第k小。   （最坏复杂度k*logK)

```java
    public class Solution {
       public int kthSmallest(int[][] matrix, int k) {
           int n = matrix.length;
           PriorityQueue<Tuple> pq = new PriorityQueue<Tuple>();
           for(int j = 0; j <= n-1; j++) 
               pq.offer(new Tuple(0, j, matrix[0][j]));
           for(int i = 0; i < k-1; i++) {
               Tuple t = pq.poll();
               if(t.x == n-1) continue;
               pq.offer(new Tuple(t.x+1, t.y, matrix[t.x+1][t.y]));
           }
           return pq.poll().val;
       }
    }

    class Tuple implements Comparable<Tuple> {
       //需要比较，实现comparable接口中的conpareTo方法
       int x, y, val;
       public Tuple (int x, int y, int val) {
           this.x = x;
           this.y = y;
           this.val = val;
       }
       
       @Override
       public int compareTo (Tuple that) {
           return this.val - that.val;  
       }
    }
```
