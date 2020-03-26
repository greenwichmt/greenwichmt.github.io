---
layout: post
title: leetcode 938. Range Sum of BST
date: 2020-03-26 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode]
---

# 题目：在二叉搜索树里找符合条件的节点值并求和
Given the root node of a binary search tree, return the sum of values of all nodes with value between L and R (inclusive).
The binary search tree is guaranteed to have unique values.
```
Example 1:
Input: root = [10,5,15,3,7,null,18], L = 7, R = 15
Output: 32
Example 2:
Input: root = [10,5,15,3,7,13,18,1,null,6], L = 6, R = 10
Output: 23
```

Note:
The number of nodes in the tree is at most 10000.
The final answer is guaranteed to be less than 2^31.

# 我的答案
```java
    public int rangeSumBST(TreeNode root, int L, int R) {
        if (root == null) {
            return 0;
        }
        int valToAdd = (root.val < L || root.val > R) ? 0 : root.val;
        return valToAdd + rangeSumBST(root.left, L, R) + rangeSumBST(root.right, L, R);
    }
```
# 我的分析
这道题很简单，本质上是二叉树的遍历，前中后序都可以
