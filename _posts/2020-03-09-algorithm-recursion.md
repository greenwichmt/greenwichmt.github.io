---
layout: post
title: clarify the concept of recursion
date: 2020-03-09 20:30
author: admin
comments: true
categories: [algorithm]
tags: [algorithm,leetcode]
---

# Clarify the concept of recursion

What's the difference and connections between recursion, divide-and-conquer algorithm, dynamic programming, and greedy algorithm? If you haven't made it clear. Doesn't matter! I would give you a brief introduction to kick off this section.

Recursion is a programming technique. It's a way of thinking about solving problems. There're two algorithmic ideas to solve specific problems: divide-and-conquer algorithm and dynamic programming. They're largely based on recursive thinking (although the final version of dynamic programming is rarely recursive, the problem-solving idea is still inseparable from recursion). There's also an algorithmic idea called greedy algorithm which can efficiently solve some more special problems. And it's a subset of dynamic programming algorithms.

The divide-and-conquer algorithm will be explained in this section. Taking the most classic merge sort as an example, it continuously divides the unsorted array into smaller sub-problems. This is the origin of the word “divide and conquer”. Obviously, the sub-problems decomposed by the ranking problem are non-repeating. If some of the sub-problems after decomposition are duplicated (the nature of overlapping sub-problems), then the dynamic programming algorithm is used to solve them!

## Recursion in detail

Before introducing divide and conquer algorithm, we must first understand the concept of recursion.

The basic idea of ​​recursion is that a function calls itself directly or indirectly, which transforms the solution of the original problem into many smaller sub-problems of the same nature. All we need is to focus on how to divide the original problem into qualified sub-problems, rather than study how this sub-problem is solved. The difference between recursion and enumeration is that enumeration divides the problem horizontally and then solves the sub-problems one by one, but recursion divides the problem vertically and then solves the sub-problems hierarchily.

The following illustrates my understanding of recursion. ** If you don't want to read, please just remember how to answer these questions: **

1. How to sort a bunch of numbers? Answer: Divided into two halves, first align the left half, then the right half, and finally merge. As for how to arrange the left and right half, please read this sentence again.
2. How many hairs does Monkey King have? Answer: One plus the rest.
3. How old are you this year? Answer: One year plus my age of last year, I was born in 1999.

Two of the most important characteristics of recursive code: **end conditions and self-invocation**. Self-invocation is aimed at solving sub-problems, and the end condition defines the answer to the simplest sub-problem.


```c++
int func(How old are you this year) {
    // simplest sub-problem, end condition
    if (this year equals 1999) return my age 0;
    // self-calling to decompose problem
    return func(How old are you last year) + 1;   
}
```

Actually think about it, what is the most successful application of recursion? I think it's mathematical induction. ** We all learned mathematical induction in high school. The usage scenario is probably: we can't figure out a summation formula, but we tried a few small numbers, it seemed to find a little law, and then compiled a formula to see It should be the correct answer. But mathematics is very rigorous. Even if you count 10,000 counts, it's correct, but is the 10,000th one correct? This requires mathematical induction to exert its power. It can be assumed that the formula we compiled is true at the kth number. If it is proved that it is true at the k + 1th, then the formula we have compiled is correct.

So what is the connection between mathematical induction and recursion? We just said that the recursive code must have an end condition, if not, it will enter endless self-calls until the memory is exhausted. The difficulty of mathematical proof is that you can try to have a finite number of cases, but it is difficult to extend your conclusion to infinity. Here you can see the connection-infinite.

The essence of recursive code is to call itself to solve smaller sub-problems until the end condition is reached. The reason why mathematical induction is useful is to continuously increase our guess by one, and expand the size of the conclusion. There is no end condition, so By extending the conclusion to infinity, the proof of the correctness of the guess is completed.
