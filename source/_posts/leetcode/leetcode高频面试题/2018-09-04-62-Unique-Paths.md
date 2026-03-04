---
title: 62. Unique Paths
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-04 10:02:01
subtitle:
tags:
  -	Math
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/unique-paths/

### 题目描述

​	There is a robot on an `m x n` grid. The robot is initially located at the **top-left corner** (i.e., `grid[0][0]`). The robot tries to move to the **bottom-right corner** (i.e., `grid[m - 1][n - 1]`). The robot can only move either down or right at any point in time.

Given the two integers `m` and `n`, return *the number of possible unique paths that the robot can take to reach the bottom-right corner*.

The test cases are generated so that the answer will be less than or equal to `2 * 109`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

```
Input: m = 3, n = 7
Output: 28
```

**Example 2:**

```
Input: m = 3, n = 2
Output: 3
Explanation: From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Down -> Down
2. Down -> Down -> Right
3. Down -> Right -> Down
```

 

**Constraints:**

- `1 <= m, n <= 100`

### 题目解析

- 这个题实际就是求排列组合，能想到这就简单，不然就吃力了，因为是矩型，而且只能左到右，上到下的走，所以从开始点到结束点，从左到右，从上到下 一定都是固定的步数。
- 排列组合的计算，方法数有很多，主要是用到简化公式的方法，不然直接计算 很容易就溢出了，至于计算方法，待我其它文章详细总结。

### 代码

```java
class Solution {
    public static int uniquePaths(int m, int n) {
        return (int)C2(m+n-2,m-1);
    }
   public static int C2(int n,int m){
        long res = 1;
        for (int i = 1;i <= m;i++){
            res =res*(n-m+i)/i;
        }
        return (int)res;
    }
}
```

