---
title: 152. Maximum Product Subarray
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-05 10:59:18
subtitle:
tags:
  - Dynamic Programming
  - Array
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/maximum-product-subarray/

### 题目描述

​	Given an integer array `nums`, find a contiguous non-empty subarray within the array that has the largest product, and return *the product*.

The test cases are generated so that the answer will fit in a **32-bit** integer.

A **subarray** is a contiguous subsequence of the array.

 

**Example 1:**

```
Input: nums = [2,3,-2,4]
Output: 6
Explanation: [2,3] has the largest product 6.
```

**Example 2:**

```
Input: nums = [-2,0,-1]
Output: 0
Explanation: The result cannot be 2, because [-2,-1] is not a subarray.
```

 

**Constraints:**

- `1 <= nums.length <= 2 * 104`
- `-10 <= nums[i] <= 10`
- The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

### 题目解析

- 最大乘积子数组，dp_max[i]表示 以i结尾的最大乘积子数组，dp_min[i]表示以i结尾的最小乘积子数组,主要是数值可能是负的，可能会出现负负得正的情况，那我们可以避开这种详细的讨论，因为答案一定在与dp_min数组或者dp_max数组乘积中可以得出。

### 代码

```java
class Solution {
   public static int maxProduct(int[] nums) {
        if(nums.length == 1) return nums[0];
        int min_pre = nums[0];
        int max_pre = nums[0];
        int res = max_pre;
        for (int i = 1; i < nums.length; i++) {
            int tmp = Math.min(Math.min(nums[i],min_pre*nums[i]),nums[i]*max_pre);
            max_pre = Math.max(Math.max(nums[i],max_pre*nums[i]),nums[i]*min_pre);
            min_pre = tmp;
            res = Math.max(res,max_pre);
        }
        return res;
    }
}
```

