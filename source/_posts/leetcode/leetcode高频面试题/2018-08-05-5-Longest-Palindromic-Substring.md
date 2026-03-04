---
title: 5. Longest Palindromic Substring
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-05 09:30:39
subtitle:
tags:
  - Dynamic Programming
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/longest-palindromic-substring/

### 题目描述

​	Given a string `s`, return *the longest palindromic substring* in `s`.

 

**Example 1:**

```
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
```

**Example 2:**

```
Input: s = "cbbd"
Output: "bb"
```

 

**Constraints:**

- `1 <= s.length <= 1000`
- `s` consist of only digits and English letters.

### 题目解析

- 此题是比较经典的DP问题，可以先对边界进行单独处理，转移方程如下:
  $$
  dp[i][j] = \begin{cases} true & i = j\\equal(chars[i],chars[j]) & j=i+1,j < N  \end{cases}
  $$
  然后，再对非边界内元素进行递推即可:
  $$
  dp[i][j] = \begin{cases}  if (chars[i]=chars[j])  & dp[i+1][j-1]
  \\ else & false 
  \end{cases}
  $$
  

### 代码

```java
class Solution {
   public static String longestPalindrome(String s) {
        int res = 1;
        int sd=0,ed=0;
        final char[] chars = s.toCharArray();
        int N = s.length();
        final boolean[][] dp = new boolean[N][N];
        for (int i = 0;i < N;i++){
            dp[i][i] = true;
            if (i+1 < N){
                dp[i][i+1] = chars[i]==chars[i+1];
                if(dp[i][i+1]){
                        res = 2;
                        sd = i;
                        ed = i+1;
                    }
                }
            }
        for (int i = N-3;i >= 0;i--)
            for (int j = i+2;j < N;j++){
                if (chars[i] == chars[j]){
                    dp[i][j] = dp[i+1][j-1];
                }
                if(dp[i][j]){
                    if(j-i+1 > res){
                        res = j-i+1;
                        sd = i;
                        ed = j;
                    }
                }
            }
        return s.substring(sd,ed+1);
    }
}
```

