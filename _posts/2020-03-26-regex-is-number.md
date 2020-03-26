---
layout: post
title: leetcode 65. Valid Number
date: 2020-03-26 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode]
---

# 题目：判断有效数字
Validate if a given string can be interpreted as a decimal number.
``` scala
Some examples:
"0" => true
" 0.1 " => true
"abc" => false
"1 a" => false
"2e10" => true
" -90e3   " => true
" 1e" => false
"e3" => false
" 6e-1" => true
" 99e2.5 " => false
"53.5e93" => true
" --6 " => false
"-+3" => false
"95a54e53" => false
```
Note: It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one. However, here is a list of characters that can be in a valid decimal number:

Numbers 0-9  
Exponent - "e"  
Positive/negative sign - "+"/"-"  
Decimal point - "."  
Of course, the context of these characters also matters in the input.

# 我的答案
```java
    public static boolean isNumber(String s) {
        String regex = "^[+-]?((\\d*\\.?\\d+)|(\\d+\\.?\\d*))(e[+-]?\\d+)?$";
        return s.trim().matches(regex);
    }
```
# 我的分析
正则表达式还是挺容易写的，但要注意一些极端的场景
