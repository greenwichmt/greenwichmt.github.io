---
layout: post
title: leetcode 1137. N-th Tribonacci Number
date: 2020-03-26 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode,recursion]
---

# 题目：获取斐波那契数列,实际上是triple吹波那契数列:)
The Tribonacci sequence Tn is defined as follows: 
T0 = 0, T1 = 1, T2 = 1, and Tn+3 = Tn + Tn+1 + Tn+2 for n >= 0.
Given n, return the value of Tn.
```
Example 1:
Input: n = 4
Output: 4
Explanation:
T_3 = 0 + 1 + 1 = 2
T_4 = 1 + 1 + 2 = 4
Example 2:
Input: n = 25
Output: 1389537
```
Constraints:
0 <= n <= 37
The answer is guaranteed to fit within a 32-bit integer, ie. answer <= 2^31 - 1.
# 我的答案
```java
    public static int tribonacci(int n) {
        switch (n) {
            case 0:
                return 0;
            case 1:
            case 2:
                return 1;
            default:
                return tribonacci(n - 1) + tribonacci(n - 2) + tribonacci(n - 3);
        }
    }
    // 下面这个效率更高
    public int tribonacci(int n) {
        if (n < 2) return n;
        int a = 0, b = 1, c = 1, d;
        while (n-- > 2) {
            d = a + b + c;
            a = b;
            b = c;
            c = d;
        }
        return c;
    }
```
# 我的分析
***状态转移方程*** 对于每个数，它都是前面3个数的和  
***临界退出条件*** 最初始写死的3个值
