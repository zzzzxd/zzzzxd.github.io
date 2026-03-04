---
title: 279. Perfect Squares
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-27 11:15:37
subtitle:
tags:
  - LeetCode
  - Math
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/perfect-squares/

### 题目描述

​	Given an integer `n`, return *the least number of perfect square numbers that sum to* `n`.

A **perfect square** is an integer that is the square of an integer; in other words, it is the product of some integer with itself. For example, `1`, `4`, `9`, and `16` are perfect squares while `3` and `11` are not.

 

**Example 1:**

```
Input: n = 12
Output: 3
Explanation: 12 = 4 + 4 + 4.
```

**Example 2:**

```
Input: n = 13
Output: 2
Explanation: 13 = 4 + 9.
```

 

**Constraints:**

- `1 <= n <= 104`

### 题目解析

- 首先得知道 四数定理。一个数由平方数组合，不会超过4个。
- 结合打表，把，1和2的打出来，判断3的，3的没有就是4个，也挺快。

### 代码

```java
class Solution {
   public static HashSet<Integer> set = new HashSet<Integer>();
    public static HashSet<Integer> d_set = new HashSet<Integer>();
    public static int numSquares(int n) {
        if (set.size() == 0){
            for (int i = 1;i <= 100;i++){
                set.add(i*i);
                for (int j = 1;j <= i;j++){
                    d_set.add(i*i + j*j);
                }
            }
        }
        //单个平方数
        if(set.contains(n)) return 1;
        else{
            if(d_set.contains(n)) return 2;
            final Iterator<Integer> iterator = set.iterator();
            while (iterator.hasNext()){
                int t = n - iterator.next();
                if(d_set.contains(t)) return 3;
            }
            return 4;
        }
    }
}
```

