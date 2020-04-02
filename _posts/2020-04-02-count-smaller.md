---
layout: post
title: leetcode 315. 计算右侧小于当前元素的个数
date: 2020-03-26 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode,recursion]
---

# 题目：315. 计算右侧小于当前元素的个数
给定一个整数数组 nums，按要求返回一个新数组 counts。数组 counts 有该性质： counts[i] 的值是  nums[i] 右侧小于 nums[i] 的元素的数量。

示例:

输入: [5,2,6,1]
输出: [2,1,1,0] 
解释:
5 的右侧有 2 个更小的元素 (2 和 1).
2 的右侧仅有 1 个更小的元素 (1).
6 的右侧有 1 个更小的元素 (1).
1 的右侧有 0 个更小的元素.

# 我的答案
```java
    // 解法1：简单逻辑稍加优化
    public List<Integer> countSmaller(int[] nums) {
        int[] counts = new int[nums.length];
        // 从右往左遍历的好处是，可以重用已经算好的counts[j]值
        for (int i = nums.length - 1; i >= 0; i--) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[j] < nums[i]) {
                    counts[i]++;
                }
                // 这里做了一次优化，如果数字相等右面的矮个子数过了，就不用数了直接用
                if (nums[i] == nums[j]) {
                    counts[i] += counts[j];
                    break;
                }
            }
        }
        // 这里也可以用IntStream.of(counts),注意需要用boxed装箱
        return Arrays.stream(counts).boxed().collect(Collectors.toList());
    }
    
    // 解法2：采用树和递归的思想比较复杂
    public class CountSmaller315 {
    public static void main(String... args) {
        int[] nums = new int[] {5, 2, 6, 1};
        CountSmaller315 countSmaller315 = new CountSmaller315();
        List<Integer> counted = countSmaller315.countSmaller(nums);
        PrintUtil.printline(counted);
    }

    public List<Integer> countSmaller(int[] nums) {
        int n = nums.length;
        int[] count = new int[n];
        TreeNode treeNode = null;
        for (int i = n - 1; i >= 0; i--) {
            // 给定值返回比它小的数量是多少，如果在当前节点返回当前节点的smallerCount，如果小于当前节点看它的左子树，如果大于当前节点看右子树
            count[i] = countSmaller(treeNode, nums[i]);
            // 把值附到节点上，如果当前值小于节点值放左子树（同时 treeNode.smallerCount++），否则放右子树
            treeNode = insert(treeNode, nums[i]);
        }
        return Arrays.stream(count).boxed().collect(Collectors.toList());
    }

    /*
    给定值返回比它小的数量是多少，如果在当前节点返回当前节点的smallerCount，如果小于当前节点看它的左子树，如果大于当前节点看右子树
     */
    int countSmaller(TreeNode root, int val) {
        if (root == null) {
            return 0;
        }
        if (root.val == val) {
            return root.smallerCount;
        }
        if (val < root.val) {
            return countSmaller(root.left, val);
        }
        return 1 + root.smallerCount + countSmaller(root.right, val);
    }

    /*
    把值附到节点上，如果当前值小于节点值放左子树（同时 root.smallerCount++），否则放右子树
     */
    TreeNode insert(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(0, val);
        }
        if (val < root.val) {
            root.left = insert(root.left, val);
            root.smallerCount++;
            return root;
        } else {
            root.right = insert(root.right, val);
            return root;
        }
    }

    /*
    树的节点，节点附带比它小节点的数量
     */
    private static class TreeNode {
        int smallerCount;
        int val;
        TreeNode left;
        TreeNode right;

        public TreeNode(int smallerCount, int val) {
            this.smallerCount = smallerCount;
            this.val = val;
        }
    }
}
```
# 我的分析
这道题乍一看10分钟搞定，然而时间和空间复杂度本题是有要求的，解法1不用赘述仅仅是稍加优化简单代码。
重点说一下解法2，解题思路同样也是从右往左，针对每个值数矮子。同样利用了重用的思想，每轮数矮子的时候，countSmaller(TreeNode root, int val)函数决定了已经数完的一定能够被重用，加快了代码的运行速度！
