---
title: 76. Minimum Window Substring
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-11 10:37:55
subtitle:
tags:
  - Sliding Window
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/minimum-window-substring/

### 题目描述

​	Given two strings `s` and `t` of lengths `m` and `n` respectively, return *the **minimum window substring** of* `s` *such that every character in* `t` *(**including duplicates**) is included in the window. If there is no such substring**, return the empty string* `""`*.*

The testcases will be generated such that the answer is **unique**.

A **substring** is a contiguous sequence of characters within the string.

 

**Example 1:**

```
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
Explanation: The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t.
```

**Example 2:**

```
Input: s = "a", t = "a"
Output: "a"
Explanation: The entire string s is the minimum window.
```

**Example 3:**

```
Input: s = "a", t = "aa"
Output: ""
Explanation: Both 'a's from t must be included in the window.
Since the largest window of s only has one 'a', return empty string.
```

 

**Constraints:**

- `m == s.length`
- `n == t.length`
- `1 <= m, n <= 105`
- `s` and `t` consist of uppercase and lowercase English letters.

### 题目解析

- 尝试考虑滑动窗口，滑动窗口能使用，一定要具有单调性，能保证求解的时候，R可以不回退！比如说

  L=2，R=6，此时满足条件了，记录下来，继续考量L = 3，R= 6是否是更优的，来逼近答案，那为什么不考虑 L = 3，R = 4，5 这两个窗口呢？这两个窗口不是被跳过去了吗？因为L = 2,R = 5的时候 都不满足条件，那L=3，R= 5这个更小的窗口 怎么可能满足条件呢！所以这叫R 不回退！

- 第二点，如何判断此时窗口是满足条件的窗口，窗口内的元素是包含制定字符串元素，种类以及对应的个数。我们引入一种“欠债表”的思想，这个表内，初始化时，是我们要还的字符，也就是指定的字符串对应字符及个数，此外维护一个变量是代表 我们还欠着多少，当变量为0时，表明此窗口如何符合条件。具体看代码来理解。

### 代码

```java
class Solution {
   public static  String minWindow(String s, String t) {
        final int[] tb = new int[256]; //欠债表
        int in = 0;
        final char[] str = s.toCharArray();
        final char[] p_str = t.toCharArray();
        for (int i = 0; i < p_str.length; i++) {
            tb[p_str[i]]++;
            in++;
        }
        int min_n = Integer.MAX_VALUE;
        int sloc = 0,eloc = 0;
        for (int p1 = 0,p2 = 0;p1 < str.length;p1++){
            while (p2 < str.length && in > 0){
                if (--tb[str[p2++]] >= 0){
                    in--;
                }
            }
            if(in == 0){
                if(p2 - p1 < min_n){
                    min_n = p2-p1;
                    sloc = p1;
                    eloc = p2;
                }
            }
            if (++tb[str[p1]] > 0){ in++; }
        }
        return s.substring(sloc,eloc);
    }
}
```

