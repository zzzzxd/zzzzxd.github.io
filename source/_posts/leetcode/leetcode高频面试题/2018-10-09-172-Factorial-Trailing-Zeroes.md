---
title: 172. Factorial Trailing Zeroes
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-09 18:10:29
subtitle:
tags:
  - Math
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/factorial-trailing-zeroes/

### 题目描述

​	Given an integer `n`, return *the number of trailing zeroes in* `n!`.

Note that `n! = n * (n - 1) * (n - 2) * ... * 3 * 2 * 1`.

 

**Example 1:**

```
Input: n = 3
Output: 0
Explanation: 3! = 6, no trailing zero.
```

**Example 2:**

```
Input: n = 5
Output: 1
Explanation: 5! = 120, one trailing zero.
```

**Example 3:**

```
Input: n = 0
Output: 0
```

 

**Constraints:**

- `0 <= n <= 104`

### 题目解析

- 这道题是与质数有关的题目，一个数的介乘分解质因数后有多少个质数a？这个是典型的题目
- 此题就是问，分解后有多少个5，因为2 * 5 = 10，而2的质数远比5多啊，所以取决于5

### 代码

```java
class Solution {
   public static int trailingZeroes(int n) {
        int res = 0;
        while (n != 0){
            res += n/5;
            n /= 5;
        }
        return res;
    }
}
```

