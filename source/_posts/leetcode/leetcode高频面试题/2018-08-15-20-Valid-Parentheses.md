---
title: 20. Valid Parentheses
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-15 12:55:59
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/valid-parentheses/

### 题目描述

​	Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.

 

**Example 1:**

```
Input: s = "()"
Output: true
```

**Example 2:**

```
Input: s = "()[]{}"
Output: true
```

**Example 3:**

```
Input: s = "(]"
Output: false
```

 

**Constraints:**

- `1 <= s.length <= 104`
- `s` consists of parentheses only `'()[]{}'`

### 题目解析

- 很简单，栈的最基本应用

### 代码

```java
class Solution {
    public static boolean isValid(String s) {
		if (s == null || s.length() == 0) {
			return true;
		}
		char[] str = s.toCharArray();
		int N = str.length;
		char[] stack = new char[N];
		int size = 0;
		for (int i = 0; i < N; i++) {
			char cha = str[i];
			if (cha == '(' || cha == '[' || cha == '{') {
				stack[size++] = cha == '(' ? ')' : (cha == '[' ? ']' : '}');
			} else {
				if (size == 0) {
					return false;
				}
				char last = stack[--size];
				if (cha != last) {
					return false;
				}
			}
		}
		return size == 0;
	}
}
```

