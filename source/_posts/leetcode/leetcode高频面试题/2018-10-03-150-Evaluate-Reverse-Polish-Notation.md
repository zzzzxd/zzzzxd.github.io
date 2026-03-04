---
title: 150. Evaluate Reverse Polish Notation
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-03 10:52:55
subtitle:
tags:
  -	Stack
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/evaluate-reverse-polish-notation/

### 题目描述

​	Evaluate the value of an arithmetic expression in [Reverse Polish Notation](http://en.wikipedia.org/wiki/Reverse_Polish_notation).

Valid operators are `+`, `-`, `*`, and `/`. Each operand may be an integer or another expression.

**Note** that division between two integers should truncate toward zero.

It is guaranteed that the given RPN expression is always valid. That means the expression would always evaluate to a result, and there will not be any division by zero operation.

 

**Example 1:**

```
Input: tokens = ["2","1","+","3","*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9
```

**Example 2:**

```
Input: tokens = ["4","13","5","/","+"]
Output: 6
Explanation: (4 + (13 / 5)) = 6
```

**Example 3:**

```
Input: tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]
Output: 22
Explanation: ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 17 + 5
= 22
```

 

**Constraints:**

- `1 <= tokens.length <= 104`
- `tokens[i]` is either an operator: `"+"`, `"-"`, `"*"`, or `"/"`, or an integer in the range `[-200, 200]`.

### 题目解析

- 就是计算后缀表达式

### 代码

```java
class Solution {
    public static int evalRPN(String[] tokens) {
         int [] stack = new int[tokens.length];
        int top = -1;
        for (int i = 0; i < tokens.length; i++) {
            String c = tokens[i];
            switch (c){
                case "+":{
                    final int a = stack[top--];
                    final int b = stack[top--];
                    stack[++top] = a + b;
                    break;
                }
                case "-":{
                    final int a = stack[top--];
                    final int b = stack[top--];
                    stack[++top] = b-a;
                    break;
                }
                case "*":{
                    final int a = stack[top--];
                    final int b = stack[top--];
                    stack[++top] = a * b;
                    break;
                }
                case "/" : {
                    final int a = stack[top--];
                    final int b = stack[top--];
                    stack[++top] = b /a ;
                    break;
                }
                default:{
                    //操作数
                    final int v = Integer.parseInt(c);
                    stack[++top] = v;
                    break;
                }
            }
        }
        return (int)stack[top];
    }
}
```

