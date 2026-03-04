---
title: 38. Count and Say
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-21 17:37:53
subtitle:
tags:
  - recursion
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/count-and-say/

### 题目描述

​	The **count-and-say** sequence is a sequence of digit strings defined by the recursive formula:

- `countAndSay(1) = "1"`
- `countAndSay(n)` is the way you would "say" the digit string from `countAndSay(n-1)`, which is then converted into a different digit string.

To determine how you "say" a digit string, split it into the **minimal** number of substrings such that each substring contains exactly **one** unique digit. Then for each substring, say the number of digits, then say the digit. Finally, concatenate every said digit.

For example, the saying and conversion for digit string `"3322251"`:

![img](https://assets.leetcode.com/uploads/2020/10/23/countandsay.jpg)

Given a positive integer `n`, return *the* `nth` *term of the **count-and-say** sequence*.

 

**Example 1:**

```
Input: n = 1
Output: "1"
Explanation: This is the base case.
```

**Example 2:**

```
Input: n = 4
Output: "1211"
Explanation:
countAndSay(1) = "1"
countAndSay(2) = say "1" = one 1 = "11"
countAndSay(3) = say "11" = two 1's = "21"
countAndSay(4) = say "21" = one 2 + one 1 = "12" + "11" = "1211"
```

 

**Constraints:**

- `1 <= n <= 30`

### 题目解析

-  很多题目只是表面花里胡哨，读懂了，把题目简化一下，就发现时一个很普通的模型，此题就属于这类，写好递归即可。

### 代码

```java
class Solution {
    public static String countAndSay(int n) {
		if (n < 1) {
			return "";
		}
		if (n == 1) {
			return "1";
		}
		char[] last = countAndSay(n - 1).toCharArray();
		StringBuilder ans = new StringBuilder();
		int times = 1;
		for (int i = 1; i < last.length; i++) {
			if (last[i - 1] == last[i]) {
				times++;
			} else {
				ans.append(times);
				ans.append(last[i - 1]);
				times = 1;
			}
		}
		ans.append(times);
		ans.append(last[last.length - 1]);
		return ans.toString();
	}
}
```

