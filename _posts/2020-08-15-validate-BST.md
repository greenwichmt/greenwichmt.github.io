---
layout: post
title: leetcode 98. 验证二叉搜索树
date: 2020-08-15 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode]
---

# 题目：在二叉搜索树里找符合条件的节点值并求和
给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

1. 节点的左子树只包含小于当前节点的数。
2. 节点的右子树只包含大于当前节点的数。
3. 所有左子树和右子树自身必须也是二叉搜索树。
```
Example 1:
输入:
    5
   / \
  1   4
     / \
    3   6
输出: false
解释: 输入为: [5,1,4,null,null,3,6]。
     根节点的值为 5 ，但是其右子节点值为 4 。
```

# 我的答案
```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) {
            return true;
        } else if (root.left == null && null == root.right) {
            return true;
        } else if (root.left != null && null == root.right) {
            return  maxValLeft(root.left) < root.val && isValidBST(root.left);
        } else if (root.left == null && null != root.right) {
            return root.val < minValRight(root.right) && isValidBST(root.right);
        } else {
            // 左右分别都是BST，且左子树最大值 < 当前 node.val < 右子树最小值
            return maxValLeft(root.left) < root.val
                && root.val < minValRight(root.right) && isValidBST(root.left) && isValidBST(root.right);
        }
    }

    private int maxValLeft(TreeNode left) {
        if (left.right == null) {
            return left.val;
        } else {
            return maxValLeft(left.right);
        }
    }

    private int minValRight(TreeNode right) {
        if (right.left == null) {
            return right.val;
        } else {
            return maxValLeft(right.left);
        }
    }
}
```
# 我的分析
这道题首先分析二叉搜索树的特征，根据题目中的3个特征可以在代码里完全拷贝
