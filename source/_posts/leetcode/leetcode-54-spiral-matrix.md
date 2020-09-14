---
layout: post
title: "Spiral Matrix"
date: 2017-03-28 13:03
comments: true
tags: 
	- Java 
	- LeetCode 
	- 矩阵
---

题目描述
------------------
给出一个M X N的矩阵，以螺旋方式给出其序列。
Given a matrix of m x n elements (m rows, n columns), return all elements of the matrix in spiral order.

For example,
Given the following matrix: 
```
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
```
<!-- more -->

You should return [1,2,3,6,9,8,7,4,5]. 

[原题地址](https://leetcode.com/problems/spiral-matrix/)
```java
class Solution {
    public static List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> list = new LinkedList<>();
        boolean flagSub = true;
        if(matrix.length == 0) {
            return list;
        }

        do {
            boolean [][]flag = new boolean[matrix.length][matrix[0].length];

            for(int i = 0; i < matrix[0].length; i++) {
                list.add(matrix[0][i]);
                flag[0][i] = true;
            }

            for (boolean[] e: flag) {
                for (boolean q : e) {
                    q = false;
                }
            }

            for(int i = 1; i < matrix.length; i++) {
                if (!flag[i][matrix[0].length - 1]) {
                    list.add(matrix[i][matrix[0].length - 1]);
                    flag[i][matrix[0].length - 1] = true;
                }
            }

            for(int i = matrix[0].length - 2; i >= 0; i--) {// i = 0的边界条件
                if(!flag[matrix.length-1][i]) {
                    list.add(matrix[matrix.length - 1][i]);
                    flag[matrix.length - 1][i] = true;
                }
            }

            for(int i = matrix.length - 2; i > 0; i--) {
                if(!flag[i][0]) {
                    list.add(matrix[i][0]);
                    flag[i][0] = true;
                }
            }


            flagSub = matrix.length >2 && matrix[0].length >2;
            if(flagSub) {
                matrix = getSubMatrix(matrix);
            }
        }while(flagSub);

        return list;
    }

    public static int[][] getSubMatrix(int[][] matrix) {
        int[][] subMatrix = new int[matrix.length - 2][matrix[0].length -2];
        for(int i = 1; i < matrix.length - 1; i ++) {
            for(int j = 1; j < matrix[0].length - 1; j++) {
                subMatrix[i - 1][j - 1] = matrix[i][j];
            }
        }
        return subMatrix;
    }
}
```

这种做法比较蠢，有大佬这样做，用方向矩阵的方式来做：
```java
/*
    Solution 3:
    使用方向矩阵来求解
    */
    
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> ret = new ArrayList<Integer>();
        if (matrix == null || matrix.length == 0 
            || matrix[0].length == 0) {
            return ret;   
        }
        
        int rows = matrix.length;
        int cols = matrix[0].length;
        
        int visitedRows = 0;
        int visitedCols = 0;

        // indicate the direction of x    
        
        // 1: means we are visiting the row by the right direction.
        // -1: means we are visiting the row by the left direction.
        int[] x = {1, 0, -1, 0};
        
        // 1: means we are visiting the colum by the down direction.
        // -1: means we are visiting the colum by the up direction.
        int[] y = {0, 1, 0, -1};
        
        // 0: right, 1: down, 2: left, 3: up.
        int direct = 0;
        
        int startx = 0;
        int starty = 0;
        
        int candidateNum = 0;
        int step = 0;
        while (true) {
            if (x[direct] == 0) {
                // visit Y axis.
                candidateNum = rows - visitedRows;
            } else {
                // visit X axis
                candidateNum = cols - visitedCols;
            }
            
            if (candidateNum <= 0) {
                break;
            }
            
            ret.add(matrix[startx][starty]);
            step++;
            
            if (step == candidateNum) {
                step = 0;
                visitedRows += x[direct] == 0 ? 0: 1;
                visitedCols += y[direct] == 0 ? 0: 1;
                
                // move forward the direction.
                direct ++;
                direct = direct%4;
            }
            
            // 根据方向来移动横坐标和纵坐标。
            startx += y[direct];
            starty += x[direct];
        }
        
        return ret;
    }
```