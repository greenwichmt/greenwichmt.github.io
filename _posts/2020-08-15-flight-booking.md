---
layout: post
title: leetcode 938. Range Sum of BST
date: 2020-08-15 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode,recursion]
---

# 题目 1109. 航班预订统计

这里有 n 个航班，它们分别从 1 到 n 进行编号。
我们这儿有一份航班预订表，表中第 i 条预订记录 bookings[i] = [i, j, k] 意味着我们在从 i 到 j 的每个航班上预订了 k 个座位。
请你返回一个长度为 n 的数组 answer，按航班编号顺序返回每个航班上预订的座位数。

```
Example 1:

输入：bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
输出：[10,55,45,25,25]
```

# 我的答案
```java
public class FlightBooking1109 {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] occurs = new int[n];
        for (int[] booking : bookings) {
            occurs[booking[0] - 1] += booking[2];
            if (booking[1] < n) {
                occurs[booking[1]] -= booking[2];
            }
        }
        for (int i = 1; i < occurs.length; i++) {
            occurs[i] += occurs[i - 1];
        }
        return occurs;
    }
}
```
# 我的分析
这道题考察的是公交车上下车算法，记录每个时间点上下车分别多少人，然后用增量的方法得到当前的存量状态
