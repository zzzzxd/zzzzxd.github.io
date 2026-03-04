---
title: 334. Increasing Triplet Subsequence
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-09 22:24:13
subtitle:
tags:
categories:
---

### 题目链接

​	https://leetcode.com/problems/increasing-triplet-subsequence/

### 题目描述

​	Given an integer array `nums`, return `true` *if there exists a triple of indices* `(i, j, k)` *such that* `i < j < k` *and* `nums[i] < nums[j] < nums[k]`. If no such indices exists, return `false`.

 

**Example 1:**

```
Input: nums = [1,2,3,4,5]
Output: true
Explanation: Any triplet where i < j < k is valid.
```

**Example 2:**

```
Input: nums = [5,4,3,2,1]
Output: false
Explanation: No triplet exists.
```

**Example 3:**

```
Input: nums = [2,1,5,0,4,6]
Output: true
Explanation: The triplet (3, 4, 5) is valid because nums[3] == 0 < nums[4] == 4 < nums[5] == 6.
```

 

**Constraints:**

- `1 <= nums.length <= 5 * 105`
- `-231 <= nums[i] <= 231 - 1`

### 题目解析

- 这个题其实就是再问存不存在长度>=3的递增子序列。首先普通dp方法是O(N^2)的肯定过不了。
- 使用end数组，这个在优化最长递增子序列问题时时O(N*logN),大概思路时 使用end数组,end[i]代表长度为i+1的最长递增子序列结尾的最小值，那么只要i推到2时候，就满足条件了。

### 代码

```java
class Solution {
   public static  boolean increasingTriplet(int[] nums) {
        final int[] end = new int[nums.length]; //end[i] 代表 长度为i+1的子序列结尾的最小值
        int c = 0;
        for (int i = 0; i < nums.length; i++) {
            final int index = binarySearch(end, nums[i], c); //c代表长度
            if(index == c) { //说明没找到，应该扩展
                end[c++] = nums[i];
                if(c > 2) return true;
            }else
                end[index] = nums[i];
        }
        return false;
    }
    public static int binarySearch(int[] end,int x,int t){
        int l = 0,r = t-1,index = t;
        while (l <= r){
            int mid = l + (r-l)/2;
            if(end[mid] >= x){
                index = mid;
                r = mid-1;
            }else
                l = mid + 1;
        }
        return index;
    }
}
```



