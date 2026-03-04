---
title: 217. Contains Duplicate
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-25 23:02:14
subtitle:
tags:
  - LeetCode
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/contains-duplicate/

### 题目描述

​	Given an integer array `nums`, return `true` if any value appears **at least twice** in the array, and return `false` if every element is distinct.

 

**Example 1:**

```
Input: nums = [1,2,3,1]
Output: true
```

**Example 2:**

```
Input: nums = [1,2,3,4]
Output: false
```

**Example 3:**

```
Input: nums = [1,1,1,3,3,4,3,2,4,2]
Output: true
```

 

**Constraints:**

- `1 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`

### 题目解析

- 考察会不会编程。
- 发现 用for each 循环 比普通的循环要快！

### 代码

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        final HashSet<Integer> set = new HashSet<>();
//        for (int i = 0; i < nums.length; i++) {
//            if (set.contains(nums[i])) return true;
//            set.add(nums[i]);
//        }
        for(int i : nums){
            if(set.contains(i)) return true;
            set.add(i);
        }
        return false;
    }
}
```

