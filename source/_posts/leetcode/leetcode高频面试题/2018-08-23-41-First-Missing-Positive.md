---
title: 41. First Missing Positive
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-23 17:43:18
subtitle:
tags:
  - Array
  - Two Pointer
categories:
  - 算法
    - leetcode高频面试题 
---

### 题目链接

​	https://leetcode.com/problems/first-missing-positive/

### 题目描述

​	Given an unsorted integer array `nums`, return the smallest missing positive integer.

You must implement an algorithm that runs in `O(n)` time and uses constant extra space.

 

**Example 1:**

```
Input: nums = [1,2,0]
Output: 3
```

**Example 2:**

```
Input: nums = [3,4,-1,1]
Output: 2
```

**Example 3:**

```
Input: nums = [7,8,9,11,12]
Output: 1
```

 

**Constraints:**

- `1 <= nums.length <= 5 * 105`
- `-231 <= nums[i] <= 231 - 1`

### 题目解析

-  此题为hard 难度,不过确实不错！理解好题目，要求O(N),那肯定不能排序。
- 假设数组长度为N，最理想的情况就是return N+1，说明1～N 全部出现了，那如果，在我们遍历过程中，出现了一个 不在[1,N]区间的元素或者重复出现[1,N]中的元素 意味着，我们期待的N+1不可能实现了，因为有其他元素 “占位”了，此时最理想的情况是 [1,N-1]仍然满足条件，return N,所以，当扫描到异常元素时，会导致我们期望的值缩小。
- 有一点，我们怎么保证我们扫描过的元素是连续的，并且是不重复的，因为重复的会占位，所以我们用双指针，L代表扫描过有效的元素。R代表我们期望的，利用下表与值的关系来重新排列，让元素在对应的下表位置(O(N)),
- 特别注意`nums[L] == nums[nums[L]-1]`,当要置位的元素已经存在时候，说明也是垃圾值

### 代码

```java
class Solution {
     public static  int firstMissingPositive(int[] nums) {
        int L = 0; //L,R为虚指,所谓的有效区得保证有效，关键是连续，也就是扫描过去的元素得是连续的才行,L位置必须是放L+1的值
        int R = nums.length-1; //R+1代表可连续增长的最大值
        while (L <= R){
           if(nums[L]==L+1){
               L++;
           }else if(nums[L] <= L||nums[L] > R+1 ||nums[L] == nums[nums[L]-1]){
               swap(nums,L,R);
               R--;
           }else{
               swap(nums,L,nums[L]-1);
           }
        }
        return L+1;
    }
    public static void swap(int[] num,int loc1,int loc2){
        int temp = num[loc1];
        num[loc1] = num[loc2];
        num[loc2] = temp;
    }
}
```

