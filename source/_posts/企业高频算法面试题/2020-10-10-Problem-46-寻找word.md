---
title: Problem 46 寻找word
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2020-10-10 10:53:29
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

给定一个char[][] matrix，也就是char类型的二维数组，再给定一个字符串word，
可以从任何一个某个位置出发，可以走上下左右，能不能找到word？
 char[][] m = {  { 'a', 'b', 'z' }, 
                       { 'c', 'd', 'o' }, 
                       { 'f', 'e', 'o' }}
设定1：可以走重复路的情况下，返回能不能找到
比如，word = "zoooz"，是可以找到的，z -> o -> o -> o -> z，因为允许走一条路径中已经走过的字符
设定2：不可以走重复路的情况下，返回能不能找到
比如，word = "zoooz"，是不可以找到的，因为允许走一条路径中已经走过的字符不能重复走 

### 题目解析

- 从每个字符 去感染一下，普通的递归题目

### 代码

```java
// 可以走重复路
	// 从m[i][j]这个字符出发，能不能找到str[k...]这个后缀串
	public static boolean canLoop(char[][] m, int i, int j, char[] str, int k) {
		if (k == str.length) {
			return true;
		}
		if (i == -1 || i == m.length || j == -1 || j == m[0].length || m[i][j] != str[k]) {
			return false;
		}
		// 不越界！m[i][j] == str[k] 对的上的！
		// str[k+1....]
		boolean ans = false;
		if (canLoop(m, i + 1, j, str, k + 1) || canLoop(m, i - 1, j, str, k + 1) || canLoop(m, i, j + 1, str, k + 1)
				|| canLoop(m, i, j - 1, str, k + 1)) {
			ans = true;
		}
		return ans;
	}

	// 不能走重复路
	// 从m[i][j]这个字符出发，能不能找到str[k...]这个后缀串
	public static boolean noLoop(char[][] m, int i, int j, char[] str, int k) {
		if (k == str.length) {
			return true;
		}
		if (i == -1 || i == m.length || j == -1 || j == m[0].length || m[i][j] != str[k]) {
			return false;
		}
		// 不越界！也不是回头路！m[i][j] == str[k] 也对的上！
		m[i][j] = 0;
		boolean ans = false;
		if (noLoop(m, i + 1, j, str, k + 1) || noLoop(m, i - 1, j, str, k + 1) || noLoop(m, i, j + 1, str, k + 1)
				|| noLoop(m, i, j - 1, str, k + 1)) {
			ans = true;
		}
		m[i][j] = str[k];
		return ans;
	}
```

