---
layout: post
title: "Factorial Trailing Zeroes"
date: 2017-03-28 10:03
comments: true
tags: 
	- Java 
	- LeetCode 
---

题目描述
------------------

给出一个整数N，求N阶乘中有几个0。

乘法得到0就是2*5，2的数量不知比5多到哪里去了，只要计算5的数量就行，那就一直除于5，计算即可。

Given an integer n, return the number of trailing zeroes in n!.

Note: Your solution should be in logarithmic time complexity.

<!-- more -->

[原题地址](https://leetcode.com/problems/factorial-trailing-zeroes/)
```java
public class Solution {
    public int trailingZeroes(int n) {
        int count = 0;
        while(n != 0) {
            n /= 5;
            count += n;
        }
        return count;
    }
}
```
