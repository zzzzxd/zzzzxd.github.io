---
title: 371. Sum of Two Integers
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-10 22:41:58
subtitle:
tags:
  - LeetCode
  - Bit Manipulation
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/sum-of-two-integers/

### 题目描述

​	Given two integers `a` and `b`, return *the sum of the two integers without using the operators* `+` *and* `-`.

 

**Example 1:**

```
Input: a = 1, b = 2
Output: 3
```

**Example 2:**

```
Input: a = 2, b = 3
Output: 5
```

 

**Constraints:**

- `-1000 <= a, b <= 1000`

### 题目解析

- 利用位运算做加法，其实很简单，你只要利用好真值表，表示出对应和的位运算操作，进位的位元算操作，就有了

### 代码

```java
class Solution {
    public static int getSum(int a, int b) {
        int cnt = 1,elem1 = 0,elem2 = 0,jie = 0,res = 0;
        for (int i = 0;i < 32;i++){
            elem1 = (a >> i)&1;
            elem2 = (b >> i)&1;
            int t = elem1 ^ elem2 ^ jie; //当前位
            jie =jie&(elem1^elem2)|elem1&elem2;
            res |= (t<<i);
        }
        return res;
    }
}
```

