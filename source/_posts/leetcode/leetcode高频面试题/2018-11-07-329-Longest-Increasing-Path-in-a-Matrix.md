---
title: 329. Longest Increasing Path in a Matrix
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-07 22:23:23
subtitle:
tags:
  - LeetCode
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	 https://leetcode.com/problems/longest-increasing-path-in-a-matrix/

### 题目描述

​	Given an `m x n` integers `matrix`, return *the length of the longest increasing path in* `matrix`.

From each cell, you can either move in four directions: left, right, up, or down. You **may not** move **diagonally** or move **outside the boundary** (i.e., wrap-around is not allowed).

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/01/05/grid1.jpg)

```
Input: matrix = [[9,9,4],[6,6,8],[2,1,1]]
Output: 4
Explanation: The longest increasing path is [1, 2, 6, 9].
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/01/27/tmp-grid.jpg)

```
Input: matrix = [[3,4,5],[3,2,6],[2,2,1]]
Output: 4
Explanation: The longest increasing path is [3, 4, 5, 6]. Moving diagonally is not allowed.
```

**Example 3:**

```
Input: matrix = [[1]]
Output: 1
```



**Constraints:**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 200`
- `0 <= matrix[i][j] <= 231 - 1`

### 题目解析

- 遍历一下，配合上缓存，查找最长的路径即可

### 代码

```java
class Solution {
    public static int[] dirx = new int[]{-1,0,1,0};
    public static int[] diry = new int[]{0,1,0,-1};
    public static int longestIncreasingPath(int[][] matrix) {
        int[][] dp = new int[matrix.length][matrix[0].length];
        int maxn = -1;
        for (int i = 0; i < matrix.length; i++) {
            for (int i1 = 0; i1 < matrix[0].length; i1++) {
                maxn = Math.max(maxn,process(matrix,i,i1,-1, dp));
            }
        }
        return maxn;
    }
    public static int process(int[][] matrix,int i,int j,int elem,int[][] dp){
        if(i < 0 || i >= matrix.length || j < 0 || j >= matrix[0].length||matrix[i][j] <= elem) { //越界
           return 0;
        }
        if(dp[i][j] != 0){
            return dp[i][j];
        }
        int res = 1;
        for (int d = 0;d < 4;d++){
                int t = process(matrix,i+dirx[d],j+diry[d],matrix[i][j],dp);
                res = Math.max(t+1,res);
        }
        dp[i][j] = res;
        return res;
    }
}
```



