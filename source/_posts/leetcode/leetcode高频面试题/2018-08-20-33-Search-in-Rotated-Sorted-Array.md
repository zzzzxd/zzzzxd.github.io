---
title: 33. Search in Rotated Sorted Array
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-20 17:10:35
subtitle:
tags:
  - Binary Search
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/search-in-rotated-sorted-array/

### 题目描述

​	There is an integer array `nums` sorted in ascending order (with **distinct** values).

Prior to being passed to your function, `nums` is **possibly rotated** at an unknown pivot index `k` (`1 <= k < nums.length`) such that the resulting array is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (**0-indexed**). For example, `[0,1,2,4,5,6,7]` might be rotated at pivot index `3` and become `[4,5,6,7,0,1,2]`.

Given the array `nums` **after** the possible rotation and an integer `target`, return *the index of* `target` *if it is in* `nums`*, or* `-1` *if it is not in* `nums`.

You must write an algorithm with `O(log n)` runtime complexity.

 

**Example 1:**

```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

**Example 2:**

```
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```

**Example 3:**

```
Input: nums = [1], target = 0
Output: -1
```

 

**Constraints:**

- `1 <= nums.length <= 5000`
- `-104 <= nums[i] <= 104`
- All values of `nums` are **unique**.
- `nums` is an ascending array that is possibly rotated.
- `-104 <= target <= 104`

### 题目解析

- 题目不错，已经要求你log(n)的时间复杂度，那只能往二分上去想了，这是很明确的吧。
- 既然二分，关键在于根据题目的特点，找到能每次二分的点，每次能砍掉近一半的数据，此题的特点是原先是上升的，如果出现了下降的元素，那么就是发生了旋转,我们可以先通过比较l和r位置元素判断出此时是否是旋转区间，如果不是，直接二分查找，如果是可以通过l、mid、r三个位置的元素来确定哪个区间出现了旋转区间，对于无旋转区间的 可以直接判断出target是否存在此区间。
- 利用好，无旋转区间，即上升区间的特点，可以减少很多复杂的判断，最终目的就是缩小解空间，最终逼近答案

### 代码

```java
class Solution {
  public static int search(int[] nums, int target) {
        if (nums.length == 1) return nums[0] == target ? 0 : -1;
        int l = 0,r = nums.length-1;
        while (l < r){
            int mid = l + (r - l)/2;
            if(nums[mid] == target) return mid;
            if(nums[l] <= nums[r]){ //l 到 r内 是递增区间，无旋转点，直接二分查找
                if(nums[mid] < target) l = mid + 1;
                else r = mid -1;
            }else{ //l 到 r内 含有旋转点 ,进一步通过mid 来定位旋转点
                if(nums[l] <= nums[mid]){ //左边无旋转点,从[l,mid]是升区间
                    if(nums[l] <= target && nums[mid] >= target){
                        //来此区间找
                        r = mid -1;
                    }else{
                        l = mid +1;
                    }
                }else{  //从mid+1到r才是正常区间
                        //左边有旋转区间,右边无旋转点，即问题变成了，此时如何判断target是在左变还是右边?通过右区间判断
                    if(nums[mid] <= target && nums[r] >=target){ //来右边
                        l = mid+1;
                    }else
                        r = mid-1;
                }
            }
        }
        return nums[l] == target ? l : -1;
    }
}
```

