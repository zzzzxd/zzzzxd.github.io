---
title: 42. Trapping Rain Water
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-26 18:38:56
subtitle:
tags:
  - Two Pointer
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/trapping-rain-water/

### 题目描述

​	Given `n` non-negative integers representing an elevation map where the width of each bar is `1`, compute how much water it can trap after raining.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)

```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.
```

**Example 2:**

```
Input: height = [4,2,0,3,2,5]
Output: 9
```

 

**Constraints:**

- `n == height.length`
- `1 <= n <= 2 * 104`
- `0 <= height[i] <= 105`

### 题目解析

- 乍一看，以为是单调栈，单调栈最大的特点就是，能求以当前元素为最小值的最长区间，这个题实际上并不适合，所以说上来先不要急着套模型，先从题目本身出发，去思考如何解决。
- 每个柱子都有可能接住雨水，接的大小实际就是它能“涨”多高，不就是取决于它左边最高高度和右面最高高度的最小值么。想明白这个地方，这个题就有了。
- 构造出辅助数组left[i],表示从0~i中的最大值，right[i]，表示i~lenth-1中的最大值，其中left可以再第二次遍历求每个柱子接水的时候一并算出，这样两个循环就搞定了。

### 代码

```java
class Solution {
    public static int trap(int[] height) {
        if(height.length == 1) return 0;
        final int[] right = new int[height.length];
        int max_n = height[height.length-1];
        right[height.length-1] = max_n;
        for (int i = height.length-2;i >= 0;i--){
            max_n = Math.max(height[i],max_n);
            right[i] = max_n;
        }
        int res = 0;
        max_n = height[0];
        for (int i = 1;i < height.length-1;i++){
            int expect = Math.min(max_n,right[i+1]);
            res += expect < height[i] ? 0 : expect-height[i];
            max_n = Math.max(max_n,height[i]);
        }
        return res;
    }
}
```

