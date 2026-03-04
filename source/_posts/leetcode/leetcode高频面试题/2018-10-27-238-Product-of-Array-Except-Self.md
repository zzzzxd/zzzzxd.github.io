---
title: 238. Product of Array Except Self
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-27 23:47:38
subtitle:
tags:
  - LeetCode
  - Array
categories:
---

### 题目链接

​	https://leetcode.com/problems/product-of-array-except-self/

### 题目描述

​	Given an integer array `nums`, return *an array* `answer` *such that* `answer[i]` *is equal to the product of all the elements of* `nums` *except* `nums[i]`.

The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

You must write an algorithm that runs in `O(n)` time and without using the division operation.

 

**Example 1:**

```
Input: nums = [1,2,3,4]
Output: [24,12,8,6]
```

**Example 2:**

```
Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]
```

 

**Constraints:**

- `2 <= nums.length <= 105`
- `-30 <= nums[i] <= 30`
- The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

### 题目解析

- 题目不难，我觉得这种小题就是好题，培养分析问题的思维。
- 提前构造一个辅助数组表示从右面的累成，然后从左遍历，保留左边的累成，这样就可以求出每个元素两边的累乘。

### 代码

```java
class Solution {
  public static int[] productExceptSelf(int[] nums) {
        int[] suffix = new int[nums.length];
        suffix[nums.length-2] = nums[nums.length-1];
        suffix[nums.length-1] = 1;
        for (int i = suffix.length-3; i >= 0; i--) {
            suffix[i] = suffix[i+1] * nums[i+1];
        }
        int pre = nums[0];
        for (int i = 1;i < nums.length;i++){
            suffix[i] = suffix[i] * pre;
            pre *= nums[i];
        }
        return suffix;
    }
}
```



