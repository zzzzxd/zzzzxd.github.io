---
title: 169. Majority Element
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-05 11:22:47
subtitle:
tags:
  -	Array
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/majority-element/

### 题目描述

​	Given an array `nums` of size `n`, return *the majority element*.

The majority element is the element that appears more than `⌊n / 2⌋` times. You may assume that the majority element always exists in the array.

 

**Example 1:**

```
Input: nums = [3,2,3]
Output: 3
```

**Example 2:**

```
Input: nums = [2,2,1,1,1,2,2]
Output: 2
```

 

**Constraints:**

- `n == nums.length`
- `1 <= n <= 5 * 104`
- `-109 <= nums[i] <= 109`

### 题目解析

- 水王问题、摩尔投票问题都是一类，这个考察的是，如果存在大于一半的元素，至多是一个，同理推导，对于N个元素中，要求个数大于 N/k的元素，这样元素至多是k-1个，不可能是k个。

### 代码

```java
class Solution {
    public int majorityElement(int[] nums) {
        int c = 1,elem = nums[0];
        for (int i = 1; i < nums.length; i++) {
            if(nums[i] == elem){
                c++;
            }else{
                if(--c <= 0){
                    c = 1;
                    elem = nums[i];
                }
            }
        }
        return  elem;
    }
}
```

