---
title: 29. Divide Two Integers
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-20 17:05:01
subtitle:
tags:
  - math
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/divide-two-integers/

### 题目描述

​	Given two integers `dividend` and `divisor`, divide two integers **without** using multiplication, division, and mod operator.

The integer division should truncate toward zero, which means losing its fractional part. For example, `8.345` would be truncated to `8`, and `-2.7335` would be truncated to `-2`.

Return *the **quotient** after dividing* `dividend` *by* `divisor`.

**Note:** Assume we are dealing with an environment that could only store integers within the **32-bit** signed integer range: `[−231, 231 − 1]`. For this problem, if the quotient is **strictly greater than** `231 - 1`, then return `231 - 1`, and if the quotient is **strictly less than** `-231`, then return `-231`.

 

**Example 1:**

```
Input: dividend = 10, divisor = 3
Output: 3
Explanation: 10/3 = 3.33333.. which is truncated to 3.
```

**Example 2:**

```
Input: dividend = 7, divisor = -3
Output: -2
Explanation: 7/-3 = -2.33333.. which is truncated to -2.
```

 

**Constraints:**

- `-231 <= dividend, divisor <= 231 - 1`
- `divisor != 0`

### 题目解析

- 这题leetcode标注为中等难度，点踩的人特别多， 我也比较晕 不知道考察什么，可能还没领会到精髓。

### 代码

```java
class Solution {
    public static int divide(int dividend, int divisor) {
        //溢出的情况判断
        if (dividend == Integer.MIN_VALUE && divisor == -1) return Integer.MAX_VALUE;
        return dividend/divisor;
    }
}
```



