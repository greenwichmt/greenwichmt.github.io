---
layout: post
title: leetcode 1160. 拼写单词
date: 2020-03-28 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode,map]
---

# 题目：1160. 拼写单词
给你一份『词汇表』（字符串数组） words 和一张『字母表』（字符串） chars。
假如你可以用 chars 中的『字母』（字符）拼写出 words 中的某个『单词』（字符串），那么我们就认为你掌握了这个单词。
注意：每次拼写（指拼写词汇表中的一个单词）时，chars 中的每个字母都只能用一次。
返回词汇表 words 中你掌握的所有单词的 长度之和。

示例 1：
输入：words = ["cat","bt","hat","tree"], chars = "atach"
输出：6
解释： 
可以形成字符串 "cat" 和 "hat"，所以答案是 3 + 3 = 6。

示例 2：
输入：words = ["hello","world","leetcode"], chars = "welldonehoneyr"
输出：10
解释：
可以形成字符串 "hello" 和 "world"，所以答案是 5 + 5 = 10。

# 我的答案
```java
    public int countCharacters(String[] words, String chars) {
        Map<Character, Long> groupCharCntMap = getMapFromString(chars);
        return Arrays.stream(words)
            .filter(e -> canBeSpelled(e, groupCharCntMap))
            .mapToInt(String::length)
            .reduce(0, Integer::sum);
    }

    private boolean canBeSpelled(String e, Map<Character, Long> groupCharCntMap) {
        Map<Character, Long> smallCharCntMap = getMapFromString(e);
        for (Map.Entry<Character, Long> e1 : smallCharCntMap.entrySet()) {
            if (!groupCharCntMap.containsKey(e1.getKey()) || groupCharCntMap.get(e1.getKey()) < e1.getValue()) {
                return false;
            }
        }
        return true;
    }

    private Map<Character, Long> getMapFromString(String e) {
        Map<Character, Long> smallCharCntMap = new HashMap<>();
        for (Character character : e.toCharArray()) {
            smallCharCntMap.merge(character, 1L, Long::sum);
        }
        return smallCharCntMap;
    }
```
# 我的分析
简单，但我的答案不是最优解，本题还可以和最终结果两两比较，先对目标chararray排序，然后byte之间比对
