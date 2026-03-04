---
title: 240. Search a 2D Matrix II
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-27 00:03:34
subtitle:
tags:
  - LeetCode
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/search-a-2d-matrix-ii/

### 题目描述

​	Write an efficient algorithm that searches for a value `target` in an `m x n` integer matrix `matrix`. This matrix has the following properties:

- Integers in each row are sorted in ascending from left to right.
- Integers in each column are sorted in ascending from top to bottom.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/24/searchgrid2.jpg)

```
Input: matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 5
Output: true
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/11/24/searchgrid.jpg)

```
Input: matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 20
Output: false
```

 

**Constraints:**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= n, m <= 300`
- `-109 <= matrix[i][j] <= 109`
- All the integers in each row are **sorted** in ascending order.
- All the integers in each column are **sorted** in ascending order.
- `-109 <= target <= 109`

### 题目解析

- 两个方向都是递增的，明显提示二分了，我们可以一个方向遍历，再另一个方向用二分
- 法一不好，没有充分利用二分特性！其实想清楚，只要在右上角，则可以不断的二分！

### 代码

```java
class Solution {
   public boolean searchMatrix(int[][] matrix, int target) {
        int M = matrix.length;
        int N = matrix[0].length;
        if (target < matrix[0][0] || target > matrix[M-1][N-1]) return false;
        if (target == matrix[0][0] || target == matrix[M-1][N-1]) return true;
        for (int i = M-1 ;i >= 0;i--){
            if(matrix[i][0] > target){
                continue;
            }
            if(binarySearch(matrix[i],target)){
                return true;
            }
        }
        return false;
    }
    public boolean binarySearch(int[] num,int target){
        int l = 0,r = num.length-1;
        while (l < r){
            int mid = l + (r- l)/2;
            if(num[mid] == target) return true;
            else if(num[mid] < target) l = mid+1;
            else r = mid -1;
        }
        return num[l] == target;
    }
}
```

```java
class Solution {
  public boolean searchMatrix(int[][] matrix, int target){
        int M = matrix.length,N = matrix[0].length;
        int l = 0,r = N-1;
        while (inEdge(l,r,M,N)){
            if(matrix[l][r] == target) return true;
            else if (matrix[l][r] > target){
                r -= 1;
            }else
                l += 1;
        }
        return false;
    }
    public static boolean inEdge(int l,int r,int m,int n){
        return l >= 0 && l < m && r >= 0 && r < n;
    }
}
```

