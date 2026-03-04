---
title: 171. Excel Sheet Column Number
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-08 17:34:00
subtitle:
tags:
  -	Array
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/excel-sheet-column-number/

### 题目描述

​	Given a string `columnTitle` that represents the column title as appears in an Excel sheet, return *its corresponding column number*.

For example:

```
A -> 1
B -> 2
C -> 3
...
Z -> 26
AA -> 27
AB -> 28 
...
```

 

**Example 1:**

```
Input: columnTitle = "A"
Output: 1
```

**Example 2:**

```
Input: columnTitle = "AB"
Output: 28
```

**Example 3:**

```
Input: columnTitle = "ZY"
Output: 701
```

 

**Constraints:**

- `1 <= columnTitle.length <= 7`
- `columnTitle` consists only of uppercase English letters.
- `columnTitle` is in the range `["A", "FXSHRXW"]`.

### 题目解析

- 就是简单的映射

### 代码

```java
class Solution {
   public static int titleToNumber(String columnTitle) {
        final char[] chars = columnTitle.toCharArray();
        int res = 0;
        for (int i = 0; i < chars.length; i++) {
            res = res * 26 + (chars[i] - 'A' + 1);
        }
        return res;
    }
}
```

