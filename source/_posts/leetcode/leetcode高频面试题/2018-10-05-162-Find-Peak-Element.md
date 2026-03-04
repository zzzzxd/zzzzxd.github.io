---
title: 162. Find Peak Element
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-05 11:16:16
subtitle:
tags:
  - Binary Search
categories:
  - 算法
    - leetcode高频面试题  
---

### 题目链接

​	https://leetcode.com/problems/find-peak-element/

### 题目描述

​	A peak element is an element that is strictly greater than its neighbors.

Given a **0-indexed** integer array `nums`, find a peak element, and return its index. If the array contains multiple peaks, return the index to **any of the peaks**.

You may imagine that `nums[-1] = nums[n] = -∞`. In other words, an element is always considered to be strictly greater than a neighbor that is outside the array.

You must write an algorithm that runs in `O(log n)` time.

 

**Example 1:**

```
Input: nums = [1,2,3,1]
Output: 2
Explanation: 3 is a peak element and your function should return the index number 2.
```

**Example 2:**

```
Input: nums = [1,2,1,3,5,6,4]
Output: 5
Explanation: Your function can return either index number 1 where the peak element is 2, or index number 5 where the peak element is 6.
```

 

**Constraints:**

- `1 <= nums.length <= 1000`
- `-231 <= nums[i] <= 231 - 1`
- `nums[i] != nums[i + 1]` for all valid `i`.

### 题目解析

- 要求log(N)，提示很明确了二分。

### 代码

```java
class Solution {
   public static int findPeakElement(int[] nums) {
        if (nums.length == 1) return 0;
        int l = 0,r = nums.length-1;
        while (l < r){
            int mid = l + (r - l)/2;
            final int res = isPeek(nums, mid);
            if(res == 0) return mid;
            else if(res < 0) r = mid-1;
            else l = mid+1;
        }
        return l;
    }

    private static int isPeek(int[] nums, int mid) {
        if (mid == 0 ){
            return nums[mid+1] < nums[mid] ? 0 : 1;
        }else if (mid == nums.length-1){
            return nums[mid] > nums[mid-1] ? 0 : -1;
        }
        else {
            if(nums[mid-1] < nums[mid] && nums[mid] < nums[mid+1]) return 1; //往右走
            else if(nums[mid-1] > nums[mid] && nums[mid] > nums[mid+1]) return -1; //往左走
            else if(nums[mid-1] > nums[mid] && nums[mid] < nums[mid+1]) return 1;
            else return 0;
        }
    }
}
```



