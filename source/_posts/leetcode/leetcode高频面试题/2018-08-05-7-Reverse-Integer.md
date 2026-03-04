---
title: 7. Reverse Integer
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-05 10:02:36
subtitle:
tags:
  - math
categories:
 - 算法
   - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/reverse-integer/

### 题目描述

​	Given a signed 32-bit integer `x`, return `x` *with its digits reversed*. If reversing `x` causes the value to go outside the signed 32-bit integer range `[-2^31, 2^31 - 1]`, then return `0`.

**Assume the environment does not allow you to store 64-bit integers (signed or unsigned).**

 

**Example 1:**

```
Input: x = 123
Output: 321
```

**Example 2:**

```
Input: x = -123
Output: -321
```

**Example 3:**

```
Input: x = 120
Output: 21
```

 

**Constraints:**

- `-231 <= x <= 23^1 - 1`

### 题目解析

1. 此题偏简单，不能使用Long来存，反转之后，我们需要一位一位的进行计算存值，肯定会有溢出的情况，所以，我们要学会“提前处理”，`res==m && x%10 < o`  否则很有可能到最后是溢出的情况而我们又判断不出此时的情况。
2. 可以将传入的整数以负数形式存储，这样存的值对于反转之后Integer.MIN来说正好存下，当然此题不会传入这种值，但若是以字符串形式传入，这个技巧就生效了。

### 代码

```java
class Solution {
   public static int reverse(int x) {
        boolean neg = x < 0;
        x = neg ? x : -x;   //溢出是两个方向的，统一变负数处理，可以换成一个方向上 
        int m = Integer.MIN_VALUE / 10;
        int o = Integer.MIN_VALUE % 10;
        int res = 0;
        while (x != 0){
            if(res < m || res==m && x%10 < o) //提前进行整数溢出的判断
                return 0;
            res = res * 10 + x%10;
            x /= 10;
        }
        return neg ? res : Math.abs(res);  //理论会有 负数没溢出，转绝对值溢出的情况，所以应该特殊处理，但这个题输入是一个整数，不会出现反转是-2147483647
    }
}
```

