---
layout: post
title: "Roman to Integer"
date: 2014-12-03 17:32
comments: true
tags: 
	- Java 
	- LeetCode 
---

题目描述
------------------

给出一个罗马数字字符串，将其计算为阿拉伯数字。

Given a roman numeral, convert it to an integer.

Input is guaranteed to be within the range from 1 to 3999.

背景知识
-------------------
罗马数字是阿拉伯数字传入之前使用的一种数码。罗马数字采用七个罗马字母作数字、即Ⅰ（1）、X（10）、C（100）、M（1000）、V（5）、L（50）、D（500）。记数的方法：
    1.相同的数字连写，所表示的数等于这些数字相加得到的数，如 Ⅲ=3；
    2.小的数字在大的数字的右边，所表示的数等于这些数字相加得到的数，如 Ⅷ=8、Ⅻ=12；
    3.小的数字（限于 Ⅰ、X 和 C）在大的数字的左边，所表示的数等于大数减小数得到的数，如 Ⅳ=4、Ⅸ=9；
    4.在一个数的上面画一条横线，表示这个数增值 1,000 倍，如=5000。
<!-- more -->

[原题地址](https://leetcode.com/problems/roman-to-integer/)
```java
public class Solution {
    public int romanToInt(String s) {
        Map map = new HashMap();
        map.put('I', 1);//Key是Char就用单引号，是String就用双引号
        map.put('V', 5);
        map.put('X', 10);
        map.put('L', 50);
        map.put('C', 100);
        map.put('D', 500);
        map.put('M', 1000);
        int result = 0;
    
        for(int i = s.length() - 1; i >= 0; --i) {
            if(i == s.length() - 1) {
                result = (Integer)map.get(s.charAt(i));
                continue;
            }
            if(((Integer)map.get(s.charAt(i))) >= ((Integer)map.get(s.charAt(i + 1)))) {
                result += (Integer) map.get(s.charAt(i));
            }else {
                result -= (Integer) map.get(s.charAt(i));
            }
           
        }
        return result;
    }
}
```
