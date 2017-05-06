---
layout: post
title: "Two Sum"
date: 2014-07-01 21:52
comments: true
tags: 
	- Java 
	- LeetCode 
---


题目描述
-------

给定一个整形元素组成的数组，给出一个数，求数组中是否存在两个数之和与给定数相等。
不能使用同一个元素两次，同时一定只会有一个解。

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:

Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

<!-- more -->

[原题地址](https://leetcode.com/problems/two-sum/)

逗逼解法：
------------

```java
package com.company;

public class Main {

    public static void main(String[] args) {
    // write your code here
        int nums3[] = {3, 2, 4};
        int target3 = 6;
        int nums4[] = {2,7,11,15};
        int target4 = 9;
        int twoSum[] = Solution.twoSum(nums3, target3);
        System.out.print(twoSum[0] + " " + twoSum[1]);
        System.out.println(" finish");
    }
}

class Solution {
    public static int[] twoSum(int[] nums, int target) {
        int flag = 0;
        int array[] = {0,0};
        for(int i = 0; i < nums.length; i++) {
            if(flag == 1) {
                break;
            }

            for(int j = i + 1; j < nums.length; j++) {
                if((nums[i] + nums[j] == target)) {
                    array[0] = i;
                    array[1] = j;
                    flag = 1;
                    break;
                }
            }
        }
        return array;
    }
}
```

改进方法
--------------

```java
package com.company;

public class Main {

    public static void main(String[] args) {
    // write your code here
        int nums3[] = {3, 2, 4};
        int target3 = 6;
        int nums4[] = {2,7,11,15};
        int target4 = 9;
        int twoSum[] = Solution.twoSum(nums3, target3);
        System.out.print(twoSum[0] + " " + twoSum[1]);
        System.out.println(" finish");
    }
}

class Solution {
    public static int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
        int result[] = new int[2];
        for (int i = 0; i < nums.length; i++){
            int num = target - nums[i];
            if(map.containsKey(num)){
                result[0] = map.get(num);
                result[1] = i;
                return result;
            }
            map.put(nums[i], i);
        }
        return result;
    }
}
```