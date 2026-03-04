---
title: 118. Pascal's Triangle
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-23 23:02:02
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/pascals-triangle/

### 题目描述

​	Given an integer `numRows`, return the first numRows of **Pascal's triangle**.

In **Pascal's triangle**, each number is the sum of the two numbers directly above it as shown:

![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

 

**Example 1:**

```
Input: numRows = 5
Output: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]
```

**Example 2:**

```
Input: numRows = 1
Output: [[1]]
```

 

**Constraints:**

- `1 <= numRows <= 30`

### 题目解析

- 杨辉三角，普通循环就搞定了

### 代码

```java
class Solution {
   public static List<List<Integer>> generate(int numRows) {
        List<List<Integer>> res = new ArrayList<>();
        final ArrayList<Integer> tmp = new ArrayList<>();
        tmp.add(1);
        res.add(tmp);
        for (int i = 1;i < numRows;i++){
            final List<Integer> last = res.get(i - 1);
            final ArrayList<Integer> cur = new ArrayList<>();
            cur.add(1);
            for(int j = 1; j <= i;j++){
                cur.add(last.get(j-1) + (j >= last.size()? 0:last.get(j)));
            }
            res.add(cur);
        }
        return res;
    }
}
```

