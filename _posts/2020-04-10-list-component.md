---
layout: post
title: leetcode 817. 链表组件
date: 2020-04-10 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode]
---

# 题目：817. 链表组件
给定一个链表（链表结点包含一个整型值）的头结点 head。
同时给定列表 G，该列表是上述链表中整型值的一个子集。
返回列表 G 中组件的个数，这里对组件的定义为：链表中一段最长连续结点的值（该值必须在列表 G 中）构成的集合。
```code
示例 1：
输入: 
head: 0->1->2->3
G = [0, 1, 3]
输出: 2
解释: 
链表中,0 和 1 是相连接的，且 G 中不包含 2，所以 [0, 1] 是 G 的一个组件，同理 [3] 也是一个组件，故返回 2。
示例 2：
输入: 
head: 0->1->2->3->4
G = [0, 3, 1, 4]
输出: 2
解释: 
链表中，0 和 1 是相连接的，3 和 4 是相连接的，所以 [0, 1] 和 [3, 4] 是两个组件，故返回 2。
```
注意:
如果 N 是给定链表 head 的长度，1 <= N <= 10000。
链表中每个结点的值所在范围为 [0, N - 1]。
1 <= G.length <= 10000
G 是链表中所有结点的值的一个子集.

# 我的答案
```java
public class ListComponent {
    public int numComponents(ListNode head, int[] intArr) {
        int cnt = 0;
        boolean isLastDivision = true;
        while (null != head) {
            if (valInArr(head.val, intArr)) {
                if (isLastDivision) {
                    cnt++;
                    isLastDivision = false;
                }
            } else {
                isLastDivision = true;
            }
            head = head.next;
        }
        return cnt;
    }

    private boolean valInArr(int val, int[] intArr) {
        return IntStream.of(intArr).anyMatch(i -> i == val);
    }
}
```
# 我的分析
这道题考察的是对单链表的理解，注意相邻分隔符和临界条件就不容易出错（java8 IntStream处理起来挺慢的）
