---
title: 10. Regular Expression Matching
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-08 11:02:38
subtitle:
tags:
  - recursion
categories:
  - 算法
    - leetcode高频面试题
---

 ### 题目链接

​	https://leetcode.com/problems/regular-expression-matching/

###  题目描述

​	Given an input string `s` and a pattern `p`, implement regular expression matching with support for `'.'` and `'*'` where:

- `'.'` Matches any single character.
- `'*'` Matches zero or more of the preceding element.

The matching should cover the **entire** input string (not partial).

 

**Example 1:**

```
Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

**Example 2:**

```
Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

**Example 3:**

```
Input: s = "ab", p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```

 

**Constraints:**

- `1 <= s.length <= 20`
- `1 <= p.length <= 30`
- `s` contains only lowercase English letters.
- `p` contains only lowercase English letters, `'.'`, and `'*'`.
- It is guaranteed for each appearance of the character `'*'`, there will be a previous valid character to match.

### 题目解析

- 这个题，第一次做的的话 会比较麻烦。用i代表字符串的当前字符，j代表正则表达式的当前字符，主要分区，后面的字符是不是*这种情况，如果是\*，说明当前i未必非得让j来匹配，i和j不同要继续看i和j+2可不可能匹配，如果i后面是一串连续相同的字符，要考虑j是匹配几个着同样的字符呢？
- 进行斜率优化，其实，递归是从左到右进行的，循环是可以通过观察替换出来的。

### 代码

```java
class Solution {
    public static boolean isMatch(String s, String p) {
        return process(s.toCharArray(),0,p.toCharArray(),0);
    }

    public static boolean process(char[] str,int i,char[] exp,int j){
        if(j==exp.length) return i ==str.length;
        if(j+1 != exp.length && exp[j+1] != '*'){
            return i != str.length && (str[i]==exp[j] || exp[j]=='.') && process(str,i+1,exp,j+1);
        }else{
            if(j+1 == exp.length){
                return i+1 == str.length && (str[i]==exp[j] || exp[j]=='.');
            }else{
                int c = i;
                while (c < str.length && (str[c]==exp[j]||exp[j]=='.')){
                    if (process(str,c+1,exp,j+2)){
                        return true;
                    }
                    c++;
                }
            }
            return  process(str,i,exp,j+2);
        }
    }
}
```

```java
class Solution {
   public static boolean isMatch(String s, String p) {
        final int[][] dp = new int[s.length()+1][p.length()+1];
        return process(s.toCharArray(),0,p.toCharArray(),0,dp);
    }
    public static boolean process(char[] str,int i,char[] exp,int j,int[][] dp) {
        if(dp[i][j] != 0) return dp[i][j]==1;
        boolean res = false;
        if (j == exp.length) {
           res = i == str.length;
        }else{
            if (j + 1 != exp.length && exp[j + 1] != '*') {
                if(i != str.length && (str[i] == exp[j] || exp[j] == '.') && process2(str, i + 1, exp, j + 1,dp)){
                    res = true;
                }
            } else {
                if (j + 1 == exp.length) {
                    res =  i + 1 == str.length && (str[i] == exp[j] || exp[j] == '.');
                }else{
                    if (i == str.length) {
                        res = process2(str, i, exp, j + 2,dp);
                    } else {
                        if(str[i] != exp[j] && exp[j] != '.'){
                            res =  process2(str, i, exp, j + 2,dp);
                        }else
                            res = process2(str, i, exp, j + 2,dp) || process2(str, i+1, exp, j,dp);
                    }
                }
            }
        }
        dp[i][j] = res?1:-1;
        return res;
    }
}
```

