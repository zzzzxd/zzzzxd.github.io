---
title: 84. Largest Rectangle in Histogram
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-11 11:15:34
subtitle:
tags:
  - Monotonic Stack
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/largest-rectangle-in-histogram/

### 题目描述

​	Given an array of integers `heights` representing the histogram's bar height where the width of each bar is `1`, return *the area of the largest rectangle in the histogram*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/01/04/histogram.jpg)

```
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: The above is a histogram where width of each bar is 1.
The largest rectangle is shown in the red area, which has an area = 10 units.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/01/04/histogram-1.jpg)

```
Input: heights = [2,4]
Output: 4
```

 

**Constraints:**

- `1 <= heights.length <= 105`
- `0 <= heights[i] <= 104`

### 题目解析

- 这个是单调栈的应用，单调栈求的是，以当前值为最小值的最大区间。
- 还是从题目本身出发，先分析，分析出哪个知识点，用哪个知识点，最大的矩形面积，这个矩形一定是以某个高度为高，然后以这个高度尽可能向两边扩形成一个最大矩形。此外，这个高度一定是在所有的高度里面的，所以我们就可以遍历这些高度，对这些高度，找每个高度形成的最大区间，所有里面的最大面积就是答案。所以说，我们一定要通过分析，找到一个突破点，可以通过枚举去找到所有可能性，进而找到答案。
- 有关单调栈的实现这一块，看其它文章的总结。

### 代码

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
       int N = heights.length,top = -1,res = -1;
        final int[] stack = new int[N];
        for (int i = 0; i < heights.length; i++) {
            while (top != -1 && heights[stack[top]] >= heights[i]){
                int elem = stack[top--];
                int leftMin = top == -1 ? -1 : stack[top];
                int rightMin = i;
                int num = (rightMin - 1 - leftMin) * heights[elem];
                res =  Math.max(res,num);
            }
            stack[++top] = i;
        }
        while (top!=-1){
            int elem = stack[top--];
            int leftMin = top == -1 ? -1 : stack[top];
            int rightMin = N;
            int num = (rightMin - 1 - leftMin) * heights[elem];
            res = Math.max(res,num);
        }
        return res;
    }
}
```

