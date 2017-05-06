---
layout: post
title: "Valid Number"
date: 2016-03-23 09:21
comments: true
tags: 
	- Java 
	- LeetCode 
---


题目描述
-------

检查给定字符串是否为合法的数字。

Validate if a given string is numeric.

```
Some examples:
"0" => true
" 0.1 " => true
"abc" => false
"1 a" => false
"2e10" => true
```

<!-- more -->

Note: It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one. 

解题步骤：
-----------
先Trim去空格（原题用例前后有空格，是合法的。）
然后检查第一位的“+”， “-”。
接着分割。
可以出现科学计算法的e，然后可以出现小数点，但是都只能出现一次。
先以e分割，注意e前后都必须是整数，由于小数点可以出现在e前，所以应该对e后端进行数字校验。
同理对小数点操作。
小数点允许前后两段至多有一段不为0。
[原题地址](https://leetcode.com/problems/valid-number/)

我的做法：
------------

```java
class Solution {
    public static boolean isNumber(String s) {
        s = s.trim();
        if(s.length() == 0) {
            return false;
        }

        if(s.charAt(0) == '+' || s.charAt(0) == '-'){
            s = s.substring(1);
        }

        int loc = s.indexOf('e') >= 0 ? s.indexOf('e') : s.indexOf('E');
        if(loc > 0) {
            String sTemp = s.substring(loc + 1);
            if(sTemp.length() == 0) {
                return false;
            }
            if(sTemp.charAt(0) == '+' || sTemp.charAt(0) == '-') {
                sTemp = sTemp.substring(1);
            }
            if(!isPureDigit(sTemp)) {
                return false;
            }

            s = s.substring(0, loc);
        }else if(loc == 0) {
            return false;
        }

        int locDot = s.indexOf('.');
        if(locDot >= 0) {
            String preDot = s.substring(0, locDot);
            String postDot = s.substring(locDot + 1);
            if(preDot.isEmpty()) {
                return isPureDigit(postDot);
            }

            if(postDot.isEmpty()) {
                return isPureDigit(preDot);
            }

            return isPureDigit(preDot) && isPureDigit(postDot);
        }
        return isPureDigit(s);
    }

    public static boolean isPureDigit(String s) {
        if (s.isEmpty()) return false;
        for(int i = 0; i < s.length(); ++i) {
            if (!Character.isDigit(s.charAt(i))) {
                return false;
            }
        }
        return true;
    }
}
```