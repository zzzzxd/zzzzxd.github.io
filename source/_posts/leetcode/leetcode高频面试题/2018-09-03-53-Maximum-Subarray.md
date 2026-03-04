---
title: 53. Maximum Subarray
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-03 22:58:42
subtitle:
tags:
  - Dynamic Programming
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/maximum-subarray/

### 题目描述

​	Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return *its sum*.

A **subarray** is a **contiguous** part of an array.

 

**Example 1:**

```
Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

**Example 2:**

```
Input: nums = [1]
Output: 1
```

**Example 3:**

```
Input: nums = [5,4,-1,7,8]
Output: 23
```

 

**Constraints:**

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`

 

**Follow up:** If you have figured out the `O(n)` solution, try coding another solution using the **divide and conquer** approach, which is more subtle.

### 题目解析

- 比较常见的dp模型，从左到右,dp[i] 表示以i结尾的最大字数组的和

### 代码

```java
class Solution {
    public int maxSubArray(int[] arr) {
        final int N = arr.length;
        int pre = arr[0];
        int res = pre;
        for (int i = 1; i < arr.length; i++) {
            pre = Math.max(arr[i],arr[i]+pre);
            res = Math.max(res,pre);
        }
        return res;
    }
}
```

