---
layout: post
title: "Edit Distance"
date: 2017-03-27 21:42
comments: true
tags: 
	- Java 
	- LeetCode 
	- 字符串 
	- 动态规划 
---

题目描述
------------------

给出两个字符串，看S1最少需要几步可以变成S2？
1.可以插入一个字符。
2.可以删除一个字符。
3.可以替换一个字符。
<!-- more -->

Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

a) Insert a character
b) Delete a character
c) Replace a character


解题思路：
------------

图中表示的是，将s1:Park变成s:Spake的最小操作步数,dp[i][j] 表示从s1的前i位，变到s2的前j位，最小需要多少改变

比如我们看矩阵的第一行，分别代表从" "(空字符)变到"s", 变到"sp"，变到"spa",变到"spak"，变到"spake" 需要多少次改变，因为每次只能选择往上添加一个字符，所以累加操作数分别为1,2,3,4,5

同理，矩阵的第一列分别代表从"p" 变到” “(空字符)，从”pa“变空，从”par“变空,从"park"变空需要多少次改变，因为每次只能选择删去一个字符，所以累加操作数分别为1,2,3,4

可以参考图中绿色字体的那个例子

下箭头： 我们现在已经知道了从" " 到”s“的距离，那么从"p" 到''s" 我们只要删去这个p就行了 （Delete）

右箭头： 我们现在已经知道了从”p“到" "的距离，那么从"p" 到"s" 我们只要插入这个s就行了 （Insert）

右下箭头： 我们现在已经知道了从" "到" "的距离，那么从"p" 到"s" 我们只需要把p替换成s就行了(Replace)

注意替换的时候，如果i所代表的的字符 == j所代表的字符，那么我们便不需要做任何多余的操作（No Operation）

![](/imgs/leetcode-72-Edit-Distance/1.jpg)
[原题地址](https://leetcode.com/problems/edit-distance/)
```java
class Solution {
    public int minDistance(String word1, String word2) {
        int dp[][] = new int[word1.length() + 1][word2.length() + 1];
        dp[1][0] = 0;

        for(int i = 1; i <= word1.length(); i++) {
            dp[i][0] = dp[i - 1][0] + 1;
        }

        for(int i = 1; i <= word2.length(); i++) {
            dp[0][i] = dp[0][i - 1] + 1;
        }

        for(int i = 1; i <= word1.length(); i++) {
            for(int j = 1; j <= word2.length(); j++) {
                if(word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];  //状态转移，说明此时不需要任何操作
                }else {
                    dp[i][j] = 1 + Math.min(dp[i][j-1], Math.min(dp[i-1][j], dp[i-1][j-1]));
                }
            }
        }

        return dp[word1.length()][word2.length()];
    }
}
```
