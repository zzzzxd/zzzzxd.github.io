---
title: 3. Longest Substring Without Repeating Characters
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-04 21:01:52
subtitle:
tags:
- Sliding Window
- Dynamic Programming
categories:
- 算法
  - leetcode高频面试题

---

### 题目链接

​	https://leetcode.com/problems/longest-substring-without-repeating-characters/ 

### 题目描述

Given a string `s`, find the length of the **longest substring** without repeating characters.



**Example 1:**

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

**Example 2:**

```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```



**Constraints:**

- `0 <= s.length <= 5 * 104`
- `s` consists of English letters, digits, symbols and spaces.

### 题目解析

- 此题相对简单，属于中等难度，可以使用滑动窗口或者动态规划来做，看到最优解，习惯性的使用DP来解
- 定义dp[i]含义为以i位置结尾的最长不重复子串长度，思考如何用dp[i]推导出dp[i+1],由于是不重复子串， 所以i+1位置结尾的子串，需要考虑i+1位置自符出现的位置,可以建立一个辅助map，来记录当前字符上次出现的位置,递推方程如下:

$$
dp[i] = \left\{dp[i-1],i-map[chars[i]] \right\}\left ( i=1,2...n-1 \right )
$$

- 动态规划在分析过程中，经常是“需要什么就要创建什么"，通常要建立一些辅助数组来推导最优解

### 代码

```java
class Solution {
     public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0 || s == null) return 0;
        final char[] chars = s.toCharArray();
        int[] dp = new int[s.length()];
        int[] map = new int[256];
        for (int i = 0; i < map.length; i++) {
            map[i] = -1;
        }
        map[chars[0]] = 0;//注意记录字符最近一次出现的位置，不要漏掉首个字符
        dp[0] = 1;
        int res = 1;
        for (int i = 1; i < dp.length; i++) {
            int p1 = dp[i-1] + 1;
            int p2 = i-map[chars[i]];
            dp[i] = Math.min(p1,p2);
            res = Math.max(res,dp[i]);
            map[chars[i]]  = i;
        }
        return res;
    }
}
```

