---
title: 49. Group Anagrams
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-01 22:24:08
subtitle:
tags:
  - Hash Table
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/group-anagrams/

### 题目描述

​	Given an array of strings `strs`, group **the anagrams** together. You can return the answer in **any order**.

An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

 

**Example 1:**

```
Input: strs = ["eat","tea","tan","ate","nat","bat"]
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

**Example 2:**

```
Input: strs = [""]
Output: [[""]]
```

**Example 3:**

```
Input: strs = ["a"]
Output: [["a"]]
```

 

**Constraints:**

- `1 <= strs.length <= 104`
- `0 <= strs[i].length <= 100`
- `strs[i]` consists of lowercase English letters.

### 题目解析

- 与字母种数有关，与字母顺序无关，这种题目出现的很高，也不难，做一个词频映射表表，能表示出来哪个字符出现的次数即可
- 是26个字符，所以char[26], 即可，注意这个地方就是要用char类型数组，因为不同的频次对应不同的字符。

### 代码

```java
class Solution {
   public static List<List<String>> groupAnagrams(String[] strs) {
        HashMap<String,List<String>> map = new HashMap<String,List<String>>();
        for (int i = 0; i < strs.length; i++) {
            final char[] chars = strs[i].toCharArray();
            char[] dic = new char[26];
            for (int i1 = 0; i1 < chars.length; i1++) {
                dic[chars[i1]-'a']++;
            }
            final String dic_key = new String(dic);
            if(map.get(dic_key) == null){
                final ArrayList<String> list = new ArrayList<>();
                list.add(strs[i]);
                map.put(dic_key,list);
            }else{
                map.get(dic_key).add(strs[i]);
            }
        }
        List<List<String>> lists = new ArrayList<>();
        map.forEach((k,v)->lists.add(v));
        return lists;
    }
}
```

