---
title: Problem 52 step sum问题
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2020-10-17 11:51:17
subtitle:
tags:
  -	algorithm
  - Recursion
  -	Trie
categories:
  -	算法
    - 企业面试题
---

### 题目

​	定义何为step sum？
比如680，680 + 68 + 6 = 754，680的step sum叫754
给定一个正数num，判断它是不是某个数的step sum

### 题目解析

- 这个题目考查的二分。拿到题目务必好好观察特点。明显这个数组拆分累加之后是变大的。所以说 给一个数x，它是某个数字的step sum 取值一定在1到x之间。所以可以用二分来枚举。

### 代码

```java
public static boolean isStepSum(int stepSum) {
		int L = 0;
		int R = stepSum;
		int M = 0;
		int cur = 0;
		while (L <= R) {
			M = L + ((R - L) >> 1);
			cur = stepSum(M);
			if (cur == stepSum) {
				return true;
			} else if (cur < stepSum) {
				L = M + 1;
			} else {
				R = M - 1;
			}
		}
		return false;
	}

	public static int stepSum(int num) {
		int sum = 0;
		while (num != 0) {
			sum += num;
			num /= 10;
		}
		return sum;
	}
```

