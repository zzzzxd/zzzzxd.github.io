---
title: 387. First Unique Character in a String
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-11 23:37:53
subtitle:
tags:
  - LeetCode
categories:
---

### 题目链接

​	https://leetcode.com/problems/first-unique-character-in-a-string/

### 题目描述

​	Given a string `s`, *find the first non-repeating character in it and return its index*. If it does not exist, return `-1`.

 

**Example 1:**

```
Input: s = "leetcode"
Output: 0
```

**Example 2:**

```
Input: s = "loveleetcode"
Output: 2
```

**Example 3:**

```
Input: s = "aabb"
Output: -1
```

 

**Constraints:**

- `1 <= s.length <= 105`
- `s` consists of only lowercase English letters.

### 题目解析

- 无

### 代码

```java
class Solution {
        public int firstUniqChar(String s) {
        final int[] map = new int[256];
        final char[] chars = s.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            map[chars[i]]++;
        }
        for (int i = 0; i < chars.length; i++) {
            if(map[chars[i]] == 1) return i;
        }
        return -1;
    }
}
```



