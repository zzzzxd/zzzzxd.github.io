---
title: 287. Find the Duplicate Number
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-28 11:24:00
subtitle:
tags:
  - LeetCode
  - Array
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/find-the-duplicate-number/

### 题目描述

​	Given an array of integers `nums` containing `n + 1` integers where each integer is in the range `[1, n]` inclusive.

There is only **one repeated number** in `nums`, return *this repeated number*.

You must solve the problem **without** modifying the array `nums` and uses only constant extra space.

 

**Example 1:**

```
Input: nums = [1,3,4,2,2]
Output: 2
```

**Example 2:**

```
Input: nums = [3,1,3,4,2]
Output: 3
```

 

**Constraints:**

- `1 <= n <= 105`
- `nums.length == n + 1`
- `1 <= nums[i] <= n`
- All the integers in `nums` appear only **once** except for **precisely one integer** which appears **two or more** times.

### 题目解析

- 利用数组的特性，让每个元素归位，就会找到重复元素。

### 代码

```java
class Solution {
   public static  int findDuplicate(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            if(nums[i] == i+1)
                continue;
            while (nums[i] != i+1){
                if(nums[nums[i]-1] == nums[i])
                    return nums[i];
                swap(nums,i,nums[i]-1);
            }
        }
        return -1;
    }
    public static  void swap(int[] nums,int i,int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

