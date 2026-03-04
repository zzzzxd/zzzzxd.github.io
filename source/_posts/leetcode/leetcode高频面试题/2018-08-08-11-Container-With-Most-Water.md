---
title: 11. Container With Most Water
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-08 11:32:38
subtitle:
tags:
  -	two pointer
  - greed
  - array
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/container-with-most-water/

### 题目描述

​	You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `ith` line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return *the maximum amount of water a container can store*.

**Notice** that you may not slant the container.

 

**Example 1:**

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

```
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explanation: The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
```

**Example 2:**

```
Input: height = [1,1]
Output: 1
```

 

**Constraints:**

- `n == height.length`
- `2 <= n <= 105`
- `0 <= height[i] <= 104`

### 题目解析

- 绝对的经典问题，通过双指针，不断的推高答案，不是一下找到答案，而是尽可能的寻找到最接近答案的值

### 代码

```java
class Solution {
    public static int maxArea(int[] height) {
        int res = Integer.MIN_VALUE;
        int p1 = 0,p2 = height.length-1;
        while (p1 <p2){
            if(height[p1] < height[p2]){
               res = Math.max((p2-p1)*height[p1],res);
                p1++;
            }else{
                res = Math.max((p2-p1)*height[p2],res);
                p2--;
            }
        }
        return res;
    }
}
```

