---
title: 14. Longest Common Prefix
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-11 11:43:37
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/longest-common-prefix/

### 题目描述

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

 

**Example 1:**

```
Input: strs = ["flower","flow","flight"]
Output: "fl"
```

**Example 2:**

```
Input: strs = ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```

 

**Constraints:**

- `1 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- `strs[i]` consists of only lowercase English letters.

### 题目解析

- 遍历，不断缩短前缀即可

### 代码

```java
class Solution {
   public static String longestCommonPrefix(String[] strs) {
        if(strs.length < 0) return "";
        String commonPrefix = strs[0];
        for (int i = 1; i < strs.length; i++) {
            if(strs[i].indexOf(commonPrefix) != 0){
                final int length = commonPrefix.length();
                int j;
                for ( j = length-1; j >= 1; j--) {
                    if(strs[i].indexOf(commonPrefix.substring(0,j)) == 0){
                        commonPrefix = commonPrefix.substring(0,j);
                        break;
                    }
                }
                if(j == 0){
                    commonPrefix = "";
                    break;
                }
            }
        }
        return commonPrefix;
    }
}
```



