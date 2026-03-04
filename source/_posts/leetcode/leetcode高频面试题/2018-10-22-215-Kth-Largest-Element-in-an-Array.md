---
title: 215. Kth Largest Element in an Array
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-22 22:50:41
subtitle:
tags:
  - LeetCode
  -	Quick Sort
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/kth-largest-element-in-an-array/

### 题目描述

​	Given an integer array `nums` and an integer `k`, return *the* `kth` *largest element in the array*.

Note that it is the `kth` largest element in the sorted order, not the `kth` distinct element.

 

**Example 1:**

```
Input: nums = [3,2,1,5,6,4], k = 2
Output: 5
```

**Example 2:**

```
Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

 

**Constraints:**

- `1 <= k <= nums.length <= 104`
- `-104 <= nums[i] <= 104`

### 题目解析

- 利用快排枢轴的方法，寻找第K大元素，经典题目.

### 代码

```java
class Solution {
   public static int findKthLargest(int[] nums, int k) {
        if(nums.length == 1) return  nums[0];
        return process(nums,k,0,nums.length-1);
    }
    public static int process(int[] nums,int kth,int i,int j){
        if(i == j) {
//            System.out.println("i:"+i +" j:"+j +" kth:"+kth);
            return nums[i];
        }
        int tmp =  nums[i];
        int p1 = i,p2 = j;
        while(p1 < p2){
            while (p2 > p1 && nums[p2] < tmp){
                p2--;
            }
            if(p1 < p2){
                nums[p1] = nums[p2];
                p1++;
            }
            while (p1 < p2 && nums[p1] >= tmp){
                p1++;
            }
            if(p1 < p2){
                nums[p2] = nums[p1];
                p2--;
            }
        }
        nums[p1] = tmp; //p1位置确定
        int cur_k = p1 - i + 1;
        if(cur_k == kth) return nums[p1];
        else if(cur_k > kth) return process(nums,kth,i,p1-1);
        else return process(nums,kth-cur_k,p1+1,j);
    }
}
```

