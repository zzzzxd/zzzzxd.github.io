---
title: 54. Spiral Matrix
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-03 23:01:28
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/spiral-matrix/

### 题目描述	

​	Given an `m x n` `matrix`, return *all elements of the* `matrix` *in spiral order*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiral1.jpg)

```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiral.jpg)

```
Input: matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

 

**Constraints:**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`
- `-100 <= matrix[i][j] <= 100`

### 题目解析

- 这个题不难，也没什么特殊的知识点，就是一个技巧，用几个变量控制好 从左到右，从右到左，从上到下，从下到上的范围，循环进行即可。

### 代码

```java
class Solution {
  public static List<Integer> spiralOrder(int[][] matrix) {
        int M = matrix.length;
        int N = matrix[0].length;
        int d_left=0,d_right=N-1,d_up=0,d_down = M-1;
        int c = 0,cur_row = -1,cur_col = -1;
        final ArrayList<Integer> list = new ArrayList<>();
        while (c < M*N){
            //往右
            for (int i = d_left;i <= d_right;i++){
                list.add(matrix[d_up][i]);
                c++;
            }
            d_up++;
            //往下
            if(c >= M * N) break;
            for(int i = d_up;i <= d_down;i++){
                list.add(matrix[i][d_right]);
                c++;
            }
            d_right--;
            if(c >= M * N) break;
            //往左
            for(int i = d_right;i >= d_left;i--){
                list.add(matrix[d_down][i]);
                c++;
            }
            d_down--;
            if(c >= M * N) break;
            //往上
            for (int i = d_down;i >= d_up;i--){
                list.add(matrix[i][d_left]);
                c++;
            }
            d_left++;
        }
        return list;
    }
}
```

