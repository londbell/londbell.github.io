---
layout: post
title: "Subsets"
date: 2017-03-27 19:09
comments: true
tags: 
	- Java 
	- LeetCode 
---

题目描述
------------------

给出一个数组，每个元素均不同，给出所有他的子集

Given a set of distinct integers, nums, return all possible subsets.

Note: The solution set must not contain duplicate subsets.

For example,
If nums = [1,2,3], a solution is: 

```
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

<!-- more -->


[原题地址](https://leetcode.com/problems/subsets/)
```java
class Solution {
    public static List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> list = new LinkedList<>();
        int total = 2 << (nums.length) - 1;
        for(int i = 0;i < total; i ++) {
            List<Integer> temp = new LinkedList<>();
            for(int j = 0; j < nums.length; j++) {
                if((i & (1 << j)) != 0) {//不能写==1，注意
                    temp.add(nums[j]);
                }
            }
            list.add(temp);
        }
        return list;
    }
}
```
