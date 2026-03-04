---
title: 268. Missing Number
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-27 11:10:31
subtitle:
tags:
  - LeetCode
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/missing-number/

### 题目描述

​	Given an array `nums` containing `n` distinct numbers in the range `[0, n]`, return *the only number in the range that is missing from the array.*

 

**Example 1:**

```
Input: nums = [3,0,1]
Output: 2
Explanation: n = 3 since there are 3 numbers, so all numbers are in the range [0,3]. 2 is the missing number in the range since it does not appear in nums.
```

**Example 2:**

```
Input: nums = [0,1]
Output: 2
Explanation: n = 2 since there are 2 numbers, so all numbers are in the range [0,2]. 2 is the missing number in the range since it does not appear in nums.
```

**Example 3:**

```
Input: nums = [9,6,4,2,3,5,7,0,1]
Output: 8
Explanation: n = 9 since there are 9 numbers, so all numbers are in the range [0,9]. 8 is the missing number in the range since it does not appear in nums.
```

 

**Constraints:**

- `n == nums.length`
- `1 <= n <= 104`
- `0 <= nums[i] <= n`
- All the numbers of `nums` are **unique**.

### 题目解析

- 因为数据大小是有范围的，这种就可以利用桶排的思想。遍历两次即可。

### 代码

 ```java
 class Solution {
    public int missingNumber(int[] nums) {
         int N = nums.length; //期望的是0-N-1满，这样返回就是N
         final int[] ints = new int[N+1];
         for (int i = 0; i < nums.length; i++) {
             ints[nums[i]] = 1;
         }
         for (int i = 0;i <= N;i++){
             if(ints[i] == 0) return i;
         }
         return N;
     }
 }
 ```

