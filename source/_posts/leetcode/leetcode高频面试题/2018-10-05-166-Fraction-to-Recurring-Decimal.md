---
title: 166. Fraction to Recurring Decimal
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-05 11:21:30
subtitle:
tags:
categories:
---

 ### 题目链接

​	https://leetcode.com/problems/fraction-to-recurring-decimal/submissions/

### 题目描述

​	Given two integers representing the `numerator` and `denominator` of a fraction, return *the fraction in string format*.

If the fractional part is repeating, enclose the repeating part in parentheses.

If multiple answers are possible, return **any of them**.

It is **guaranteed** that the length of the answer string is less than `104` for all the given inputs.

 

**Example 1:**

```
Input: numerator = 1, denominator = 2
Output: "0.5"
```

**Example 2:**

```
Input: numerator = 2, denominator = 1
Output: "2"
```

**Example 3:**

```
Input: numerator = 4, denominator = 333
Output: "0.(012)"
```

 

**Constraints:**

- `-231 <= numerator, denominator <= 231 - 1`
- `denominator != 0`

### 题目解析

- 做这道题 关键是寻找循环节，可以通过余数去判断。
- 用map去记录位置，找到循环节然后插入括号。

### 代码

```java
import java.math.BigDecimal;
class Solution {
  public static String fractionToDecimal(int numerator, int denominator) {
		if (numerator == 0) {
			return "0";
		}
		StringBuilder res = new StringBuilder();
		// "+" or "-"
		res.append(((numerator > 0) ^ (denominator > 0)) ? "-" : "");
		long num = Math.abs((long) numerator);
		long den = Math.abs((long) denominator);
		// integral part
		res.append(num / den);
		num %= den;
		if (num == 0) {
			return res.toString();
		}
		// fractional part
		res.append(".");
		HashMap<Long, Integer> map = new HashMap<Long, Integer>();
		map.put(num, res.length());
		while (num != 0) {
			num *= 10;
			res.append(num / den);
			num %= den;
			if (map.containsKey(num)) {
				int index = map.get(num);
				res.insert(index, "(");
				res.append(")");
				break;
			} else {
				map.put(num, res.length());
			}
		}
		return res.toString();
	}
}
```

