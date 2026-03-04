---
title: 48. Rotate Image
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-01 22:23:03
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/rotate-image/submissions/

### 题目描述

​	You are given an `n x n` 2D `matrix` representing an image, rotate the image by **90** degrees (clockwise).

You have to rotate the image [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm), which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/08/28/mat1.jpg)

```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [[7,4,1],[8,5,2],[9,6,3]]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/08/28/mat2.jpg)

```
Input: matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
Output: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

 

**Constraints:**

- `n == matrix.length == matrix[i].length`
- `1 <= n <= 20`
- `-1000 <= matrix[i][j] <= 1000`

### 题目解析

- 第一次做还没反应过来怎么做。其实就注意一个点，是矩阵，90度旋转，它同一圈的不会转出去，所以就又一个对焦，一圈一圈转就行。

### 代码

```java
class Solution {
   public static void rotate(int[][] matrix) {
        int a = 0,b = 0,c = matrix.length-1,d = matrix.length-1;
        while (a < c){
            rotateSwap(matrix,a++,b++,c--,d--);
        }
    }
    public static void rotateSwap(int[][] matrix,int a,int b,int c,int d){
        for (int i = 0;i < d-b;i++){
            int tmp = matrix[a][b+i];
            matrix[a][b+i] = matrix[c-i][b];
            matrix[c-i][b] = matrix[c][d-i];
            matrix[c][d-i] = matrix[a+i][d];
            matrix[a+i][d] = tmp;
        }
    }
}
```



