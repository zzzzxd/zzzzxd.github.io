---
title: 125. Valid Palindrome
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-26 00:01:05
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接	

​	https://leetcode.com/problems/valid-palindrome/

### 题目描述

​	A phrase is a **palindrome** if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.

Given a string `s`, return `true` *if it is a **palindrome**, or* `false` *otherwise*.

 

**Example 1:**

```
Input: s = "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome.
```

**Example 2:**

```
Input: s = "race a car"
Output: false
Explanation: "raceacar" is not a palindrome.
```

**Example 3:**

```
Input: s = " "
Output: true
Explanation: s is an empty string "" after removing non-alphanumeric characters.
Since an empty string reads the same forward and backward, it is a palindrome.
```

 

**Constraints:**

- `1 <= s.length <= 2 * 105`
- `s` consists only of printable ASCII characters.

### 题目解析

- 题目不难，就是注意字符类型，判断好就行

### 代码

```java
class Solution {
 public static boolean isPalindrome(String s) {
		if (s == null || s.length() == 0) {
			return true;
		}
		char[] str = s.toCharArray();
		int L = 0;
		int R = str.length - 1;
		while (L < R) {
			// 英文（大小写） + 数字
			if (validChar(str[L]) && validChar(str[R])) {
				if (!equal(str[L], str[R])) {
					return false;
				}
				L++;
				R--;
			} else {
				L += validChar(str[L]) ? 0 : 1;
				R -= validChar(str[R]) ? 0 : 1;
			}
		}
		return true;
	}

	public static boolean validChar(char c) {
		return isLetter(c) || isNumber(c);
	}

	public static boolean equal(char c1, char c2) {
		if (isNumber(c1) || isNumber(c2)) {
			return c1 == c2;
		}
		// a  A   32
		// b  B   32
		// c  C   32
		return (c1 == c2) || (Math.max(c1, c2) - Math.min(c1, c2) == 32);
	}

	public static boolean isLetter(char c) {
		return (c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z');
	}

	public static boolean isNumber(char c) {
		return (c >= '0' && c <= '9');
	}
}
```

