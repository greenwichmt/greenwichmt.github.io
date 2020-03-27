---
layout: post
title: leetcode 894. All Possible Full Binary Trees
date: 2020-03-27 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode,recursion]
---

# 题目：894. 所有可能的满二叉树
A full binary tree is a binary tree where each node has exactly 0 or 2 children.  
Return a list of all possible full binary trees with N nodes.  Each element of the answer is the root node of one possible tree.  
Each node of each tree in the answer must have node.val = 0.  
You may return the final list of trees in any order.  


# 我的答案
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
    /**
     * 给定节点数量，返回所有可能的树的集合（每棵树即节点的排兵布阵）
     *
     * @param N 当前树所有节点数
     * @return 返回树的根节点列表
     */
    public List<TreeNode> allPossibleFBT(int nodeCnt) {
        List<TreeNode> treeNodeList = new ArrayList<>();
        // 如果是偶数返回空，如果是1返回1个节点的列表
        /*
         * 1. if N = 3 , the number of nodes combination are as follows
         *       left    root    right
         *        1       1        1
         * --------------N = 3, res = 1----------
         * 2. if N = 5 , the number of nodes combination are as follows
         *       left    root    right
         *        1       1        3 (recursion)
         *        3       1        1
         *   --------------N = 5, res = 1 + 1 = 2----------
         * 3. if N = 7 , the number of nodes combination are as follows
         *       left    root    right
         *        1       1        5 (recursion)
         *        3       1        3
         *        5       1        1
         *   --------------N = 7, res = 2 + 1 + 2 = 5----------
         * 4. in order to make full binary tree, the node number must increase by 2
         */
        //边界条件2:如果输入为1，那么结果就只有一个值为0的结点
        if(nodeCnt <=0 || (nodeCnt&1) == 0){
            return treeNodeList;
        }else if((nodeCnt|1) == 1){
            treeNodeList.add(new TreeNode(0));
            return treeNodeList;
        }
        //我们知道一共有N个结点，root占了1个结点，左子树可能有1，3，5，..，N-2个结点
        //对应的，右子树可能有N-2，..，5，3，1个结点
        //那么，我们可以用一个循环，找到所有可能的左右子树的可能的数量的情况，把root放进列表里
        for (int i = 1; i <= N-2; i += 2) {
            //这里就是递归的精髓了，每次看到递归，就一头雾水
            //在这里，我们不用去关心左右子树是怎么递归形成的
            //我们可以仅仅去关心，这个函数，它实现的是什么功能
            //allPossibleFBT(i)返回了一个列表，它存放着当结点数为i时，所有满足条件的树的root的集合
            //我们可以认为，allPossibleFBT(i)存放着所有满足条件的左子树的集合
            //同样，allPossibleFBT(N-1-i)存放着所有满足条件的右子树的集合
            //这是由static List<TreeNode> allPossibleFBT(int N)这个函数的定义所确定的
            List<TreeNode> leftSubTrees = allPossibleFBT(i);
            List<TreeNode> rightSubTrees = allPossibleFBT(N - i - 1);

            //接下来，就是左右子树的排列组合，当左子树为m时，右子树可能有right.size()个可能
            //所以一共有right.size() * left.size()种可能
            //我们把每一种排列，都放到我们所要的结果中
            for (TreeNode l : leftSubTrees) {
                for (TreeNode r : rightSubTrees) {
                    TreeNode root = new TreeNode(0);
                    root.left = l;
                    root.right = r;
                    //对于左子树有i个结点，右子树有N-1-i个结点时，我们把所有可能的树add到列表里
                    treeNodeList.add(root);
                }
            }
        }
        return treeNodeList;
    }
}
```
# 我的分析
解决此类问题的思路把握一个核心点，就是__把递归调用对应的函数作为一个原子功能，宏观地看待问题，寻找状态转移方程往上套__。
1. **宏观地分析递归函数的功能作用** ：*给定节点数量，返回所有可能的树的集合*（每棵树即节点的排兵布阵）
2. **状态转移方程** ：原问题可以分解为，对于节点总数为7的，它的leftChildrenTree节点总数为1,3,5 *(给定节点数量，返回所有可能的树的集合)*,它的rightChildrenTree节点总数为5,3,1 *(给定节点数量，返回所有可能的树的集合)*，然后算它们的笛卡尔积，对于每种左右子树的排兵布阵，均新建一个根节点构建一颗新的树，并加到开头的treeNodeList里面
3. **退出条件** ：分解到最后，当最小粒度子问题入参为1的时候，直接构建单个节点的树立即return


