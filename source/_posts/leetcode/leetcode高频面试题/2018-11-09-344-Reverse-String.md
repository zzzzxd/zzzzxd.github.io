---
title: 344. Reverse String
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-09 22:26:01
subtitle:
tags:
  - LeetCode
  -	Array
categories:
  - 算法
    - leetcode高频面试题

---

### 题目链接

​	https://leetcode.com/problems/reverse-string/

### 题目描述

​	Write a function that reverses a string. The input string is given as an array of characters `s`.

You must do this by modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm) with `O(1)` extra memory.

 

**Example 1:**

```
Input: s = ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]
```

**Example 2:**

```
Input: s = ["H","a","n","n","a","h"]
Output: ["h","a","n","n","a","H"]
```

### 题目解析

- 无

### 代码

```java
class Solution {
   public void reverseString(char[] s) {
        for (int i = 0; i < s.length/2; i++) {
            int t = s[i];
            s[i] = s[s.length-1-i];
            s[s.length-1-i] = (char)t;
        }
    }
}
```



