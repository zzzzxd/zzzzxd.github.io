---
title: 22. Generate Parentheses
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-15 16:17:41
subtitle:
tags:
  - recursion
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/generate-parentheses/

### 题目描述

​	Given `n` pairs of parentheses, write a function to *generate all combinations of well-formed parentheses*.

 

**Example 1:**

```
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

**Example 2:**

```
Input: n = 1
Output: ["()"]
```

 

**Constraints:**

- `1 <= n <= 8`

### 题目解析

- 普通题，考察写递归的能力。
- 要的是全部解，这种全部可能性的肯定是递归来收集答案了，注意递归的写法，确定递归函数中需要哪些信息来确定参数，要尽可能的减少参数
- 递归中，假设到[0,index-1]的已经正确的填完了，现在来填index的位置，每次填，用条件保证填对，这样到length的位置，递归结束，此时的填写的path是正确的一种，不用再进行判断。如何判断此时填什么符合？
  - 首先，leftSize表示还能填左括号的个数，开始默认是n个,那么leftSize > 0，此时可以填 左括号,填完leftSize 需要减1
  - 右括号填写的条件是，已经填的右括号的个数: $index-(N-leftSize)$  仍然小于左括号个数：$N-leftSize$,整理可得$index < 2 *(N-leftSize)$

### 代码

```java
class Solution {
   public static List<String> generateParenthesis(int n) {
        final ArrayList<String> res = new ArrayList<>();
        final StringBuilder path = new StringBuilder();
        process(n,0,res,path,n);
        return res;
    }
    public static void process(int leftRest,int index,List<String> res,StringBuilder path,int N){
        if(index == 2*N){
            res.add(path.toString());
            return;
        }
        if (leftRest > 0){
            process(leftRest-1,index+1,res,path.append("("),N);
            path.deleteCharAt(index);
        }
        if (index < 2 *(N-leftRest)){
            process(leftRest,index+1,res,path.append(")"),N);
            path.deleteCharAt(index);
        }

    }
}
```

