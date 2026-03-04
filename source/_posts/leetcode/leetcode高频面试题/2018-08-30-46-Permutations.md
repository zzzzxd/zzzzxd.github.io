---
title: 46. Permutations
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-30 22:20:45
subtitle:
tags:
  -	Recursion
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/permutations/

### 题目描述

​	Given an array `nums` of distinct integers, return *all the possible permutations*. You can return the answer in **any order**.

 

**Example 1:**

```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**Example 2:**

```
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

**Example 3:**

```
Input: nums = [1]
Output: [[1]]
```

 

**Constraints:**

- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- All the integers of `nums` are **unique**.

### 题目解析

- 全排列，基础的递归题目。

### 代码

```java
class Solution {
    public static List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> list = new ArrayList<>();
        process(list,nums,0);
        return list;
    }
    //返回[index,len-1]位置的全排列
    public static void process(List<List<Integer>> list,int[] nums,int index){
        if(index == nums.length){
            final ArrayList<Integer> tmp = new ArrayList<>();
            for (int i = 0; i < nums.length; i++) {
                tmp.add(nums[i]);
            }
            list.add(tmp);
            return;
        }
        for (int i = index;i < nums.length;i++){
            swap(nums,index,i);
            process(list,nums,index+1);
            swap(nums,index,i);
        }
    }
    public static void swap(int[] num,int i,int j){
        int t = num[i];
        num[i] = num[j];
        num[j] = t;
    }
}
```

