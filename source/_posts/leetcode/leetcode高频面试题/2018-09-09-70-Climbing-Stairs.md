---
title: 70. Climbing Stairs
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-09 10:21:58
subtitle:
tags:
  - Recursion
  - Dynamic Programming
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/climbing-stairs/

### 题目描述

​	You are climbing a staircase. It takes `n` steps to reach the top.

Each time you can either climb `1` or `2` steps. In how many distinct ways can you climb to the top?

 

**Example 1:**

```
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**

```
Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

 

**Constraints:**

- `1 <= n <= 45`

### 题目解析

-  比较经典的递归问题了，现在回过头来看，用动归的递推，还是挺简单的。

### 代码

```java
class Solution {
     public static int climbStairs(int n) {
        if(n == 1 || n == 2) return n;
        int pre1 = 2,pre2 = 1;
        for (int i = 3;i <= n;i++){
            int cur = pre1 + pre2;
            pre2 = pre1;
            pre1 = cur;
        }
        return pre1;
    }
}
```

