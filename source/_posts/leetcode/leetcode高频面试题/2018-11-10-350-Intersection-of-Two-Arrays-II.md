---
title: 350. Intersection of Two Arrays II
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-10 22:35:14
subtitle:
tags:
  - LeetCode
categories:
---

### 题目链接

​	https://leetcode.com/problems/intersection-of-two-arrays-ii/

### 题目描述

​	Given two integer arrays `nums1` and `nums2`, return *an array of their intersection*. Each element in the result must appear as many times as it shows in both arrays and you may return the result in **any order**.

 

**Example 1:**

```
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2,2]
```

**Example 2:**

```
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [4,9]
Explanation: [9,4] is also accepted.
```

 

**Constraints:**

- `1 <= nums1.length, nums2.length <= 1000`
- `0 <= nums1[i], nums2[i] <= 1000`

### 题目解析

- 题目简单，但是可以学到东西，看下数值范围，拿数组存下，再次遍历只要数组元素大于0，就统计，统计完记得减掉一个 。

### 代码

```java
class Solution {
  public int[] intersect(int[] nums1, int[] nums2) {
            final int[] map = new int[1005];
            int[] res = new int[nums1.length];
            int cnt = 0;
            for (int elem : nums1) {
              map[elem]++;
            }
            for (int elem : nums2) {
               if(map[elem]>0){
                   res[cnt++] = elem;
                   map[elem]--;
                }
            }
            return Arrays.copyOf(res,cnt);
        }
}
```

