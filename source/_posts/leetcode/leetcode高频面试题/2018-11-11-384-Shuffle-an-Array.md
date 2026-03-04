---
title: 384. Shuffle an Array
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-11 23:34:08
subtitle:
tags:
  - LeetCode
  - Array
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/shuffle-an-array/	

### 题目描述

​	Given an integer array `nums`, design an algorithm to randomly shuffle the array. All permutations of the array should be **equally likely** as a result of the shuffling.

Implement the `Solution` class:

- `Solution(int[] nums)` Initializes the object with the integer array `nums`.
- `int[] reset()` Resets the array to its original configuration and returns it.
- `int[] shuffle()` Returns a random shuffling of the array.

 

**Example 1:**

```
Input
["Solution", "shuffle", "reset", "shuffle"]
[[[1, 2, 3]], [], [], []]
Output
[null, [3, 1, 2], [1, 2, 3], [1, 3, 2]]

Explanation
Solution solution = new Solution([1, 2, 3]);
solution.shuffle();    // Shuffle the array [1,2,3] and return its result.
                       // Any permutation of [1,2,3] must be equally likely to be returned.
                       // Example: return [3, 1, 2]
solution.reset();      // Resets the array back to its original configuration [1,2,3]. Return [1, 2, 3]
solution.shuffle();    // Returns the random shuffling of array [1,2,3]. Example: return [1, 3, 2]
```

 

**Constraints:**

- `1 <= nums.length <= 50`
- `-106 <= nums[i] <= 106`
- All the elements of `nums` are **unique**.
- At most `104` calls **in total** will be made to `reset` and `shuffle`.

### 题目解析

- 利用随机交换就可以，注意踩坑.

### 代码

```java
class Solution {
    int[] cap;
    Random random = new Random();
    public Solution(int[] nums) {
        cap = Arrays.copyOf(nums, nums.length);
    }
    public int[] reset() {
        return Arrays.copyOf(cap,cap.length);
    }
    public int[] shuffle() {
        final int[] t = Arrays.copyOf(cap, cap.length);
        for (int i = t.length-1; i> 0; i--) {
            final int loc = random.nextInt(i+1);//  踩坑
            swap(t,i,loc);
        }
        return t;
    }
    public void swap(int[] nums,int i,int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(nums);
 * int[] param_1 = obj.reset();
 * int[] param_2 = obj.shuffle();
 */
```

