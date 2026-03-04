---
title: 44. Wildcard Matching
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-28 18:49:49
subtitle:
tags:
  - Dynamic Programming
  - Recursion
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/wildcard-matching/

### 题目描述

​	Given an input string (`s`) and a pattern (`p`), implement wildcard pattern matching with support for `'?'` and `'*'` where:

- `'?'` Matches any single character.
- `'*'` Matches any sequence of characters (including the empty sequence).

The matching should cover the **entire** input string (not partial).

 

**Example 1:**

```
Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

**Example 2:**

```
Input: s = "aa", p = "*"
Output: true
Explanation: '*' matches any sequence.
```

**Example 3:**

```
Input: s = "cb", p = "?a"
Output: false
Explanation: '?' matches 'c', but the second letter is 'a', which does not match 'b'.
```



**Constraints:**

- `0 <= s.length, p.length <= 2000`
- `s` contains only lowercase English letters.
- `p` contains only lowercase English letters, `'?'` or `'*'`.

### 题目解析

- 题目是通配符匹配，用动态规划会简单点, i,j的含义是s的前0~i字符能否与p的0~j字符匹配,然后根据p[j]是什么样的字符进行可能性的枚举即可。
- 

### 代码

```java
class Solution {
    public static boolean isMatch(String s, String p) {
        if("**".equals(p) || "*".equals(p)){
            return true;
        }
        final char[] str = s.toCharArray();
        final char[] p_str = p.toCharArray();
        final int[][] dp = new int[str.length + 1][p_str.length + 1];
        return process(str,p_str,s.length()-1,p.length()-1,dp);
    }
    public static boolean process(char[] str,char[] p,int i,int j,int[][] dp){

        if(dp[i+1][j+1] != 0) {
            return dp[i+1][j+1]==1;
        }
        if(j == -1){
            dp[i+1][j+1] = i==-1?1:-1;
            return dp[i+1][j+1]==1;
        }
        if(i == -1){
            for (int k = j;k >=0;k--){
                if(p[k] != '*'){
                    dp[i+1][j+1] = -1;
                    return false;
                }
            }
            dp[i+1][j+1] = 1;
            return true;
        }
        if(p[j] == '*'){
            for (int k = i;k >= -1;k--){
                if(process(str,p,k,j-1,dp)){
                    dp[i+1][j+1] = 1;
                    return true;
                }
            }
        }else if(p[j] == '?'){
            if (process(str,p,i-1,j-1,dp)){
                dp[i+1][j+1] = 1;
                return true;
            }
           
        }else {
            if(str[i]==p[j]&&process(str,p,i-1,j-1,dp)){
                dp[i+1][j+1] = 1;
                return true;
            }
        }
        dp[i+1][j+1] = -1;
        return false;
    }
}
```

