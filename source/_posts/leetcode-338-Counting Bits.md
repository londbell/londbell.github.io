---
layout: post
title: "Factorial Trailing Zeroes"
date: 2016-03-27 10:03
comments: true
tags: 
	- Java 
	- LeetCode 
	- 动态规划 
	- 位操作 
---

题目描述
------------------
大意就是实现求给出比num小的所有数的二进制位中1的位数，隐藏提示是使用动态规划。

Given a non negative integer number num. For every numbers i in the range 0 ≤ i ≤ num calculate the number of 1’s in their binary representation and return them as an array.
Example:

For num = 5 you should return [0,1,1,2,1,2].
Follow up:

It is very easy to come up with a solution with run time O(n*sizeof(integer)). But can you do it in linear time O(n) /possibly in a single pass?
Space complexity should be O(n).
Can you do it like a boss? Do it without using any builtin function like __builtin_popcount in c++ or in any other language.

<!-- more -->
[原题地址](https://leetcode.com/problems/counting-bits/)
逗逼解法
---------
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

动态规划
----------
需要注意到的是，每隔2^n的大小，就会刚好多出一位，也就是说构造出动态规划的叠加数组，在以前计算过的基础上加1，即可得到后面数的“1”位数。

```java
public class Solution {
     public static int[] countBits(int num) {
        if(num == 0) return new int[]{0};
        int[] result = new int[num + 1];
        int len, count = 0;
        while(true){
            len = count + 1;
            for(int i = 0; i < len; i++){
                ++count;
                result[count] = result[i] + 1;             
                if(count >= num)
                    return result;
            }
        }
    }
}
```