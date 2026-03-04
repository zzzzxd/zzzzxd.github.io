---
title: 131. Palindrome Partitioning
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-27 07:09:41
subtitle:
tags:
  - Dynamic Programming
  - Recursion
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/palindrome-partitioning/

### 题目描述

​	Given a string `s`, partition `s` such that every substring of the partition is a **palindrome**. Return all possible palindrome partitioning of `s`.

A **palindrome** string is a string that reads the same backward as forward.

 

**Example 1:**

```
Input: s = "aab"
Output: [["a","a","b"],["aa","b"]]
```

**Example 2:**

```
Input: s = "a"
Output: [["a"]]
```

 

**Constraints:**

- `1 <= s.length <= 16`
- `s` contains only lowercase English letters.

### 题目解析

- 中等难度。要求我们输出所有的切分之后每一块都是回文串的可能性，要枚举第一刀切在哪，要保证切出来的是回文串，切完第一刀之后，我们可以递归去收集答案，第一刀切出来的这个部分是不是回文串还要判断
- 为了加速判断，可以事先用DP把回文串的下标求出来，加速判断过程，但怪异的事，这种做法竟然不如现判断的效果好，八成leetcode的测试用例不行。

### 代码

```java
//事先dp 求解回文串下标
class Solution {
 public List<List<String>> partition(String s) {
        final char[] chars = s.toCharArray();
        final int N = s.length();
        final List<List<String>> res = new ArrayList<>();
        final ArrayList<String> list = new ArrayList<>();
        final boolean[][] dp = new boolean[N][N];
        for(int i = 0;i < N;i++){
            dp[i][i]  = true;
            if(i+1 < N)
                dp[i][i+1] = chars[i] == chars[i+1];
        }
        for (int i = N-3;i >=0 ;i--){
            for(int j = i+2;j < N;j++){
                if(chars[i] != chars[j]) dp[i][j] = false;
                else
                    dp[i][j] = dp[i+1][j-1];
            }
        }
        process(res,list,0,s,dp);
        return res;
    }
    public void process(List<List<String>> res,List<String> path,int index,String str,boolean[][] dp){
        int N = str.length();
        if(index == N){
            final ArrayList<String> list = new ArrayList<>(path);
            res.add(list);
            return;
        }
        for (int i = index;i < N;i++){
            if (dp[index][i]){
                path.add(str.substring(index,i+1));
                process(res,path,i+1,str,dp);
                path.remove(path.size()-1);
            }
        }
    }
}
//现判断
class Solution {
 public List<List<String>> partition(String s) {
        
        List<List<String>> result = new ArrayList<>();
        partitioning(0, s, result, new ArrayList<>());
        return result;
    }
    
    private void partitioning(int index, String s, List<List<String>> result, List<String> ds){
        
        if (index == s.length()){
            result.add(new ArrayList<>(ds));
            return;
        }
        
        for (int i = index; i < s.length(); i++){
            if (isPalindrome(s, index, i)){
                ds.add(s.substring(index, i+1));
                partitioning(i+1, s, result, ds);
                ds.remove(ds.size()-1);
            }
        }
    }
    
    private boolean isPalindrome(String s, int start, int end){
        while (start <= end){
            if (s.charAt(start) != s.charAt(end)){
                return false;
            }
            start++;
            end--;
        }
        return true;
    }
}
```

