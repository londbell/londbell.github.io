---
layout: post
title: "search a 2dmatrix"
date: 2015-09-01 15:21
comments: true
tags: 
	- Java 
	- LeetCode 
	- 矩阵
---


题目描述
-------

给定一个矩阵，求特定的数是否在矩阵中。
矩阵每一行从小到大排列。每一行的最小元素比上一行最大元素大。

Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:

    Integers in each row are sorted from left to right.
    The first integer of each row is greater than the last integer of the previous row.

For example,

Consider the following matrix: 

<!-- more -->

我的做法：写起来简单，性能很辣鸡。
------------

```java
public class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if(matrix.length == 0 || matrix == null) {
            return false;
        }
        List<Integer> list = new ArrayList<>();
        for(int[] e : matrix) {
            for(int q : e) {
                list.add(q);
            }
        }
        if(list.contains(target)) {
            return true;
        }
        return false;

    }
}
```

效率高的做法:
```java
public class Solution {
  public boolean searchMatrix(int[][] matrix, int target) {
            int i = 0, j = matrix[0].length - 1;
            while (i < matrix.length && j >= 0) {
                    if (matrix[i][j] == target) {
                        return true;
                    } else if (matrix[i][j] > target) {
                        j--;
                    } else {
                        i++;
                    }
                }
            
            return false;
        }
}
```