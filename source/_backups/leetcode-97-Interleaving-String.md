---
layout: post
title: "Interleaving String"
date: 2017-03-27 20:42
comments: true
tags: 
	- Java 
	- LeetCode 
    - 动态规划
    - 字符串
---

题目描述
------------------

给出三个字符串，看S1和S2能否交叉取char字符组成S3

 Given s1, s2, s3, find whether s3 is formed by the interleaving of s1 and s2.

For example,
Given:
s1 = "aabcc",
s2 = "dbbca",

When s3 = "aadbbcbcac", return true.
When s3 = "aadbbbaccc", return false. 


<!-- more -->

解题思路：
------------

DP[i][j]存的是取S1的i个字符，取S2的j个字符情况下，能否构成S3;
由于存的是“个数”，所以对应到String操作上是i+1,要注意
另外需要注意第二次运算交集时要并上本身（第一次运算说明本路径是有效的）。

![](/imgs/leetcode-97-Interleaving-String/1.jpg)
[原题地址](https://leetcode.com/problems/interleaving-string/)
```java
class Solution {
    public static boolean isInterleave(String s1, String s2, String s3) {
        if(s1 == "" || s2 == "" || s3 == "") {
            return false;
        }
        if((s1.length() + s2.length()) != s3.length()) {
            return false;
        }
        boolean[][] dp = new boolean[s1.length() + 1][s2.length() + 1];
        dp[0][0] = true;
        for(int m = 1; m < s1.length() + 1; m++) {
            if (s1.charAt(m - 1) == s3.charAt(m - 1)) {
                dp[m][0] = true && dp[m - 1][0];
            } else {
                dp[m][0] = false;
            }
        }
        for(int m = 1; m < s2.length() + 1; m++) {
            if(s2.charAt(m - 1) == s3.charAt(m - 1)) {
                dp[0][m] = true && dp[0][m - 1];
            }else{
                dp[0][m] = false;
            }
        }
        for(int m = 1; m < s1.length() + 1; m++) {
            for(int n = 1; n < s2.length() + 1; n++) {
                if(s1.charAt(m - 1) == s3.charAt(m + n - 1)) {
                    dp[m][n] = true && dp[m-1][n];
                }
                if(s2.charAt(n - 1) == s3.charAt(m + n - 1)) {
                    dp[m][n] = (true && dp[m][n-1]) || dp[m][n];//防止前面的true被交集运算后得到false
                }
            }
        }
        for(int m = 0; m < s1.length() + 1; m++) {
            for(int n = 0; n < s2.length() + 1; n++) {
                //System.out.println(dp[m][n]);
            }
        }
        return dp[s1.length()][s2.length()];
    }
}

```
