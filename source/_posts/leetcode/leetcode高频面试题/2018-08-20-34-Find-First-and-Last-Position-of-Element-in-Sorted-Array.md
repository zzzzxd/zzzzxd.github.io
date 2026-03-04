---
title: 34. Find First and Last Position of Element in Sorted Array
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-20 17:25:21
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/

### 题目描述

Given an array of integers `nums` sorted in non-decreasing order, find the starting and ending position of a given `target` value.

If `target` is not found in the array, return `[-1, -1]`.

You must write an algorithm with `O(log n)` runtime complexity.

 

**Example 1:**

```
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

**Example 2:**

```
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

**Example 3:**

```
Input: nums = [], target = 0
Output: [-1,-1]
```

 

**Constraints:**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`
- `nums` is a non-decreasing array.
- `-109 <= target <= 109`

### 题目解析

- 二分的典型应用，查找第一个满足条件的值，其实 二分在求某个函数的解时，威力无穷，有机会专门总结一下。

### 代码

```java
class Solution {
  public static int[] searchRange(int[] nums, int target) {
        if(nums.length <= 1) return nums.length==0?new int[]{-1,-1} : (nums[0]==target ? new int[]{0,0} : new int[]{-1,-1});
        final int left = binarySearch(nums, target );
        if(left == Integer.MAX_VALUE||nums[left] > target) return new int[]{-1,-1};
        else{
            final int right = binarySearch(nums, target + 1);
            return new int[]{left,right == Integer.MAX_VALUE ? nums.length-1:right-1};
        }
    }
    //二分查找 查找第一个一个大于等于target的位置
    public static int binarySearch(int[] nums,int target){
        int l = 0,r =nums.length-1;
        int res = Integer.MAX_VALUE;
        while (l <= r){
            int mid = l + ((r - l)>>1);
            if(nums[mid] >= target){
                res = Math.min(res,mid);
                r = mid -1;
            }else
                l = mid +1;
        }
        return res;
    }
}
```

