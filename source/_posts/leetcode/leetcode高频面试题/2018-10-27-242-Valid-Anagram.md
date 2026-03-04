---
title: 242. Valid Anagram
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-27 00:06:43
subtitle:
tags:
  - LeetCode
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/valid-anagram/

### 题目描述

​	Given two strings `s` and `t`, return `true` *if* `t` *is an anagram of* `s`*, and* `false` *otherwise*.

An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

 

**Example 1:**

```
Input: s = "anagram", t = "nagaram"
Output: true
```

**Example 2:**

```
Input: s = "rat", t = "car"
Output: false
```

 

**Constraints:**

- `1 <= s.length, t.length <= 5 * 104`
- `s` and `t` consist of lowercase English letters.

### 题目解析

- 比较简单了。

### 代码

```java
class Solution {
    public static boolean isAnagram(String s, String t) {

        final int[] chars = new int[26];
        final char[] chars1 = s.toCharArray();
        final char[] chars2 = t.toCharArray();
        if(chars1.length != chars2.length) return false;
        for (int i = 0; i < chars1.length; i++) {
            chars[chars1[i]-'a']++;
        }
        for (int i = 0; i < chars2.length; i++) {
            if(--chars[chars2[i]-'a'] < 0){
                return false;
            }
        }
        return true;
    }
}
```

