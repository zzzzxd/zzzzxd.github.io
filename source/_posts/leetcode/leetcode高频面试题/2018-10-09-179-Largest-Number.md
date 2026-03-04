---
title: 179. Largest Number
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-09 18:24:35
subtitle:
tags:
  -	Greed
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/largest-number/

### 题目描述

​	Given a list of non-negative integers `nums`, arrange them such that they form the largest number and return it.

Since the result may be very large, so you need to return a string instead of an integer.

 

**Example 1:**

```
Input: nums = [10,2]
Output: "210"
```

**Example 2:**

```
Input: nums = [3,30,34,5,9]
Output: "9534330"
```

 

**Constraints:**

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 109`

### 题目解析

- 这个题是贪心，我们排序一下，排序时候，要不要交换，要看当前两个元素的是不交换拼接的值大，还是交换之后拼接的值大！
- 这就是贪心策略，应该多积累，见过就会，没见过就难想.

### 代码

```java
class Solution {
 public static String largestNumber(int[] nums){
        final ArrayList<String> list = new ArrayList<>();
        final StringBuilder res = new StringBuilder();
        for (int i = 0; i < nums.length; i++) {
            list.add(String.valueOf(nums[i]));
        }
        list.sort((a,b)->-(a+b).compareTo(b+a));
        for (int i = 0;i < list.size();i++){
            if(res.length() != 0 || !list.get(i).equals("0") || i == list.size()-1)
                res.append(list.get(i));
        }
        return res.toString();

    }
}
```

