---
title: 227. Basic Calculator II
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-25 23:10:26
subtitle:
tags:
  - LeetCode
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/basic-calculator-ii/submissions/

### 题目描述

​	Given a string `s` which represents an expression, *evaluate this expression and return its value*. 

The integer division should truncate toward zero.

You may assume that the given expression is always valid. All intermediate results will be in the range of `[-231, 231 - 1]`.

**Note:** You are not allowed to use any built-in function which evaluates strings as mathematical expressions, such as `eval()`.

 

**Example 1:**

```
Input: s = "3+2*2"
Output: 7
```

**Example 2:**

```
Input: s = " 3/2 "
Output: 1
```

**Example 3:**

```
Input: s = " 3+5 / 2 "
Output: 5
```

 

**Constraints:**

- `1 <= s.length <= 3 * 105`
- `s` consists of integers and operators `('+', '-', '*', '/')` separated by some number of spaces.
- `s` represents **a valid expression**.
- All the integers in the expression are non-negative integers in the range `[0, 231 - 1]`.
- The answer is **guaranteed** to fit in a **32-bit integer**.

### 题目解析

- 这道题目是一种常见的思路，乘除的时候先算，在队列里存放加减的序列,然后遍历计算一下可以。

### 代码

```java
class Solution {
  public static int calculate(String s) {
        final Deque<String> queue = new LinkedList<>();
        final char[] chars = s.toCharArray();
        int num = 0;
        for (int i = 0; i < chars.length; i++) {
             if (chars[i] == ' ') continue;
            if (chars[i] <= '9' && chars[i] >= '0') {
                num = num * 10 + (chars[i] - '0');
            } else {
                addNum(queue,num);
                queue.addLast(String.valueOf(chars[i]));
                num = 0;
            }
        }
        addNum(queue,num);
        return  getRes(queue);
    }
    public static void addNum(Deque<String> queue,int num){
        if (!queue.isEmpty() && (queue.peekLast().equals("*") || queue.peekLast().equals("/"))) {
            String exp = queue.removeLast();
            Integer a = Integer.valueOf(queue.removeLast());
            if (exp.equals("*")) {
                num = a * num;
            } else {
                num = a / num;
            }
        }
        queue.addLast(String.valueOf(num));
    }
    public static int getRes(Deque<String> queue){
        int res = Integer.valueOf(queue.removeFirst());
        while (queue.size() > 1){
            String exp = queue.removeFirst();
            String num = queue.removeFirst();
            if(exp.equals("+")){
                res += Integer.valueOf(num);
            }else
                res -= Integer.valueOf(num);
        }
        return res;
    }
}
```

