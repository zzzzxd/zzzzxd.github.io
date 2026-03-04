---
title: 128. Longest Consecutive Sequence
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-27 00:38:35
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/longest-consecutive-sequence/

### 题目描述

​	Given an unsorted array of integers `nums`, return *the length of the longest consecutive elements sequence.*

You must write an algorithm that runs in `O(n)` time.

 

**Example 1:**

```
Input: nums = [100,4,200,1,3,2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
```

**Example 2:**

```
Input: nums = [0,3,7,2,5,8,4,6,0,1]
Output: 9
```

 

**Constraints:**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`

### 题目解析

- 求最长连续序列，需要两个容器，一个装头，一个装尾，每个元素来了，要看看它前面的元素在尾里面？在的话把自己添加进去，同时存储对应前面的值，接下来再看它后面的 是头吗？如果是，需要重新更正之前的头对应后面的个数，与信息流的题目类似。head容器、tail容器。

### 代码

```java
class Solution {
    public static int longestConsecutive(int[] nums) {
        if(nums.length == 0 || nums.length == 1)return nums.length;
        final Map<Integer, Integer> head = new HashMap();
        final Map<Integer, Integer> tail = new HashMap();
        int res = 1;
        for (int i = 0; i < nums.length; i++) {
                if(head.containsKey(nums[i]) || tail.containsKey(nums[i])){
                    continue;
                }
                head.put(nums[i],1);
                tail.put(nums[i],1);
            //和前面连一块
            if (tail.containsKey(nums[i] - 1)) {
                head.remove(nums[i]);
                final Integer preLen = tail.get(nums[i] - 1);
                head.put(nums[i] - preLen, head.get(nums[i] - preLen) + 1);
                tail.put(nums[i], preLen + 1);
                res = Math.max(res,preLen+1);
            }
            //和后面连上，注意同时更新前面的连续序列
            if (head.containsKey(nums[i] + 1)) {

                final Integer posLen = head.get(nums[i] + 1);
                final Integer preLen = tail.get(nums[i]);
                head.put(nums[i]-preLen+1,preLen+posLen);
                tail.put(nums[i]+posLen,preLen+posLen);
                res = Math.max(res,preLen+posLen);
            }
        }
        return res;
    }
}
```

