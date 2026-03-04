---
title: 326. Power of Three
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-04 20:45:34
subtitle:
tags:
  - LeetCode
  - Math
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/power-of-three/

### 题目描述

​	Given an integer `n`, return *`true` if it is a power of three. Otherwise, return `false`*.

An integer `n` is a power of three, if there exists an integer `x` such that `n == 3x`.

 

**Example 1:**

```
Input: n = 27
Output: true
```

**Example 2:**

```
Input: n = 0
Output: false
```

**Example 3:**

```
Input: n = 9
Output: true
```

 

**Constraints:**

- `-231 <= n <= 231 - 1`

### 题目解析

- 可以简单的模拟，也可以用打表法，打出在int范围内，3^x的最大值，那么只要被n整除，n就是3的某次方

### 代码

```java
class Solution {
  public static boolean isPowerOfThree(int n) {
		return (n > 0 && 1162261467 % n == 0);
	}
}
```



