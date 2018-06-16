---
layout: post
title: leetcode-230-kth-smallest-element-in-a-bst
categories: [题目] 
description: leetcode-230-kth-smallest-element-in-a-bst
keywords: algorithm,二分
---


寻找二叉排序树中第k大值。  题目：[ https://leetcode.com/problems/kth-smallest-element-in-a-bst/description/](https://leetcode.com/problems/kth-smallest-element-in-a-bst/description/)    

    Given a binary search tree, write a function kthSmallest to find the <b>k</b>th smallest element in it.
    
    <b>Note: </b>
    You may assume k is always valid, 1 ≤ k ≤ BST's total elements.
    
    <b>Follow up:</b>
    What if the BST is modified (insert/delete operations) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?
    
    

 给一个二叉 搜索/排序 树。寻找它的第k大值。 二叉排序树的性质，中序遍历结果就是排好序的序列。      深度优先遍历。神奇的过了。。

```java
    /**
    * Definition for a binary tree node.
    * public class TreeNode {
    *     int val;
    *     TreeNode left;
    *     TreeNode right;
    *     TreeNode(int x) { val = x; }
    * }
    */
    class Solution {
       public int s = 0;
       public int kk = 0;
       public boolean flag = false;
       public int kthSmallest(TreeNode root, int k) {
           kk = k;
           dfs(root);
           return kk;
      }
       int dfs(TreeNode t){
           if(flag)
               return 1;
           if(t == null)
               return 0;
           dfs(t.left);
          s++;
           if(s == kk){
               kk = t.val;
               flag = true;
           }
          dfs(t.right);
           return 0;
       }
    }
```
    

  discuss中提供了 二分搜索，   深度优先搜索 的递归版和迭代版。   。。这个题应该是要用二分做的。。。毕竟放在二分分类里面。   因为二叉排序树结点的左边都小于它，右边都大于它。  先统计一个结点的左子树部分结点个数count。如果count大于等于当前的k。 说明第k大值在左半部分，不必搜索右部。否则在右边搜索第k-1-count 大值。(  1为当前结点计数。   countNodes方法用来统计结点个数。  

```java
     public int kthSmallest(TreeNode root, int k) {
           int count = countNodes(root.left);
           if (k <= count) {
               return kthSmallest(root.left, k);
           } else if (k > count + 1) {
               return kthSmallest(root.right, k-1-count); 
           }
           
           return root.val;
       }
      
       public int countNodes(TreeNode n) {
           if (n == null) return 0;
           
           return 1 + countNodes(n.left) + countNodes(n.right);
       }
    
```

 最后一个是迭代版的 dfs。  直接复制代码过来。    就是递归版做递归消除  

```java
     public int kthSmallest(TreeNode root, int k) {
           Stack<TreeNode> st = new Stack<>();
           
           while (root != null) {
               st.push(root);
               root = root.left;
           }
               
           while (k != 0) {
               TreeNode n = st.pop();
               k--;
               if (k == 0) return n.val;
               TreeNode right = n.right;
               while (right != null) {
                   st.push(right);
                   right = right.left;
               }
           }
           
           return -1; // never hit if k is valid
     }
```
