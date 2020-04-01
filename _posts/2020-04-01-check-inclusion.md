---
layout: post
title: leetcode 567. 字符串的排列
date: 2020-04-01 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode]
---

# 题目：567. 字符串的排列
给定两个字符串 s1 和 s2，写一个函数来判断 s2 是否包含 s1 的排列。
换句话说，第一个字符串的排列之一是第二个字符串的子串。
示例1:
输入: s1 = "ab" s2 = "eidbaooo"
输出: True
解释: s2 包含 s1 的排列之一 ("ba").
示例2:
输入: s1= "ab" s2 = "eidboaoo"
输出: False

注意：
输入的字符串只包含小写字母
两个字符串的长度都在 [1, 10,000] 之间

# 我的答案
```java
    public boolean checkInclusion(String s1, String s2) {
        Map<Character, Long> aimMap = getMapFromString(s1);
        Optional<String> a = IntStream.range(0, s2.length() - s1.length() + 1)
            .mapToObj(i -> s2.substring(i, s1.length() + i))
            .filter(str -> isInclusion(str, aimMap))
            .findFirst();
        return a.isPresent();
    }

    private boolean isInclusion(String str, Map<Character, Long> aimMap) {
        Map<Character, Long> checkMap = getMapFromString(str);
        Optional<Map.Entry<Character, Long>> optional = checkMap.entrySet()
            .stream()
            .filter(entry -> !aimMap.containsKey(entry.getKey()) || aimMap.get(entry.getKey()) < entry.getValue())
            .findFirst();
        return !optional.isPresent();
    }

    private Map<Character, Long> getMapFromString(String e) {
        Map<Character, Long> smallCharCntMap = new HashMap<>();
        for (Character character : e.toCharArray()) {
            smallCharCntMap.merge(character, 1L, Long::sum);
        }
        return smallCharCntMap;
    }
    
    
    // 下面这种解法更好，滑动窗口
    static int[] x;
    static int[] y;

    public static boolean checkInclusion2(String s1, String s2) {
        if (s1 == null || s2 == null || s1.length() > s2.length()) {
            return false;
        }
        x = new int[26];
        y = new int[26];
        int w = s1.length();
        for (int i = 0; i < w; i++) {
            x[s1.charAt(i) - 'a']++;
            y[s2.charAt(i) - 'a']++;
        }
        if (isAnagram()) {
            return true;
        }

        for (int i = w; i < s2.length(); i++) {
            y[s2.charAt(i - w) - 'a']--;
            y[s2.charAt(i) - 'a']++;
            if (isAnagram()) {
                return true;
            }
        }
        return false;
    }

    private static boolean isAnagram() {
        for (int i = 0; i < 26; i++) {
            if (x[i] != y[i]) {
                return false;
            }
        }
        return true;
    }
```
# 我的分析
这道题采用滑动窗口的方法解题效率更高，每滑动一次窗口，只需要把前一个滑出的减掉，加上滑入的那个
