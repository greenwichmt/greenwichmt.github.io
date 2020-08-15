---
layout: post
title: leetcode 938. Range Sum of BST
date: 2020-08-15 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode,bfs]
---

# 题目：127. 单词接龙
给定两个单词（beginWord 和 endWord）和一个字典，找到从 beginWord 到 endWord 的最短转换序列的长度。转换需遵循如下规则：

每次转换只能改变一个字母。
转换过程中的中间单词必须是字典中的单词。

说明:
如果不存在这样的转换序列，返回 0。
所有单词具有相同的长度。
所有单词只由小写字母组成。
字典中不存在重复的单词。
你可以假设 beginWord 和 endWord 是非空的，且二者不相同。

```
输入:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

输出: 5
解释: 一个最短转换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog",
     返回它的长度 5。
```

# 我的答案
```java
public class LadderLengthBFS127 {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        int step = 0;
        int wordLen = beginWord.length();
        Set<String> visited = new HashSet<>(wordList.size());
        String current = "";
        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);
        while (!queue.isEmpty()) {
            step++;
            int curLevelSize = queue.size();
            for (int i = 0; i < curLevelSize; i++) {
                current = queue.poll();
                if (endWord.equals(current)) {
                    return step;
                }
                for (String candidate : wordList) {
                    if (!visited.contains(candidate) && onlyOneCharDiff(current, candidate, wordLen)) {
                        visited.add(candidate);
                        queue.offer(candidate);
                    }
                }
            }
        }
        return 0;
    }

    private boolean onlyOneCharDiff(String current, String endWord, int wordLen) {
        int diffCnt = 0;
        for (int i = 0; i < wordLen; i++) {
            if (current.charAt(i) != endWord.charAt(i)) {
                diffCnt++;
            }
            if (diffCnt > 1) {
                return false;
            }
        }
        return true;
    }

    public static void main(String... args) {
        String beginWord = "hit";
        String endWord = "cog";
        List<String> wordList = Stream.of("hot", "dot", "dog", "lot", "log", "cog").collect(Collectors.toList());
        LadderLengthBFS127 ladder = new LadderLengthBFS127();
        printline(ladder.ladderLength(beginWord, endWord, wordList));
    }
}
```
# 我的分析
遇到最短路径应该立即想到BFS，BFS的每一层穷举所有情况(注意剪枝优化)，最早遇到的即最短路径。本题有2个注意点，1个是用step记录BFS的层数，1个是用visited哈希set记录已访问过的节点 避免产生环状图。
