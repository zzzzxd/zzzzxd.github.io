---
title: 8. String to Integer (atoi)
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-05 10:33:40
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/string-to-integer-atoi/

### 题目描述

​	Implement the `myAtoi(string s)` function, which converts a string to a 32-bit signed integer (similar to C/C++'s `atoi` function).

The algorithm for `myAtoi(string s)` is as follows:

1. Read in and ignore any leading whitespace.
2. Check if the next character (if not already at the end of the string) is `'-'` or `'+'`. Read this character in if it is either. This determines if the final result is negative or positive respectively. Assume the result is positive if neither is present.
3. Read in next the characters until the next non-digit character or the end of the input is reached. The rest of the string is ignored.
4. Convert these digits into an integer (i.e. `"123" -> 123`, `"0032" -> 32`). If no digits were read, then the integer is `0`. Change the sign as necessary (from step 2).
5. If the integer is out of the 32-bit signed integer range `[-231, 231 - 1]`, then clamp the integer so that it remains in the range. Specifically, integers less than `-231` should be clamped to `-231`, and integers greater than `231 - 1` should be clamped to `231 - 1`.
6. Return the integer as the final result.

**Note:**

- Only the space character `' '` is considered a whitespace character.
- **Do not ignore** any characters other than the leading whitespace or the rest of the string after the digits.

 

**Example 1:**

```
Input: s = "42"
Output: 42
Explanation: The underlined characters are what is read in, the caret is the current reader position.
Step 1: "42" (no characters read because there is no leading whitespace)
         ^
Step 2: "42" (no characters read because there is neither a '-' nor '+')
         ^
Step 3: "42" ("42" is read in)
           ^
The parsed integer is 42.
Since 42 is in the range [-231, 231 - 1], the final result is 42.
```

**Example 2:**

```
Input: s = "   -42"
Output: -42
Explanation:
Step 1: "   -42" (leading whitespace is read and ignored)
            ^
Step 2: "   -42" ('-' is read, so the result should be negative)
             ^
Step 3: "   -42" ("42" is read in)
               ^
The parsed integer is -42.
Since -42 is in the range [-231, 231 - 1], the final result is -42.
```

**Example 3:**

```
Input: s = "4193 with words"
Output: 4193
Explanation:
Step 1: "4193 with words" (no characters read because there is no leading whitespace)
         ^
Step 2: "4193 with words" (no characters read because there is neither a '-' nor '+')
         ^
Step 3: "4193 with words" ("4193" is read in; reading stops because the next character is a non-digit)
             ^
The parsed integer is 4193.
Since 4193 is in the range [-231, 231 - 1], the final result is 4193.
```

 

**Constraints:**

- `0 <= s.length <= 200`
- `s` consists of English letters (lower-case and upper-case), digits (`0-9`), `' '`, `'+'`, `'-'`, and `'.'`.

### 题目解析

- 这题点踩的人有点多，个人也感觉这个题不是很漂亮，主要条件描述不清楚，各种异常的情况 报错了才知道，而且也考察不出什么东西。

### 代码

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;
class Solution {
    public static int myAtoi(String s) {
        s = s.trim();
        if(s.length() ==0 || s == null) return 0;
        final char c = s.charAt(0);
        if(c<='z' && c >= 'a' || c == '.' || c <= 'Z'&& c >= 'A') return 0;
        //一定是以数字或者字符或者空白开头
            int i;
            for( i = 0;i < s.length();i++){
                if(s.charAt(i) == '+' || s.charAt(i) == '-')
                    break;
            }
          if(!(s.charAt(0) <= '9' && s.charAt(0) >= '0')){
            if(i+1 < s.length() && !(s.charAt(i+1) >='0' && s.charAt(i+1) <='9')) return 0;
        }
        String regex = "[-+]?\\d+";
        final Pattern pattern = Pattern.compile(regex);
        final Matcher matcher = pattern.matcher(s);
        if(matcher.find()){
            final String int_str = matcher.group();
            long l = 0;
            try {
                l = Integer.parseInt(int_str);
            }catch (Exception e){
                if(int_str.charAt(0) == '-')
                    return Integer.MIN_VALUE;
                else
                    return Integer.MAX_VALUE;
            }

            return (int)l;
        }
        return 0;
    }

}
```

