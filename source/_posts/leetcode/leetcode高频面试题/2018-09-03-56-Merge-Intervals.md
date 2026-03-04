---
title: 56. Merge Intervals
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-03 09:52:30
subtitle:
tags:
  -	Array
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/merge-intervals/

### 题目描述

​	Given an array of `intervals` where `intervals[i] = [starti, endi]`, merge all overlapping intervals, and return *an array of the non-overlapping intervals that cover all the intervals in the input*.

 

**Example 1:**

```
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlap, merge them into [1,6].
```

**Example 2:**

```
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.
```

 

**Constraints:**

- `1 <= intervals.length <= 104`
- `intervals[i].length == 2`
- `0 <= starti <= endi <= 104`

### 题目解析

- 题目比较简单，就是输出有多少个覆盖的区间，遍历一趟，记录下来即可

### 代码

```java
class Solution {
    public static int[][] merge(int[][] intervals) {
        int N = intervals.length;
        if(N == 1) {
            return intervals;
        }
        Arrays.sort(intervals,(a,b)->a[0]-b[0]);
        int start = intervals[0][0];
        int end = intervals[0][1];
        int t = 0;
        for (int i = 1; i < N; i++) {
            if(intervals[i][0] <= end){
                end = Math.max(intervals[i][1],end);
            }else{
                intervals[t][0] = start;
                intervals[t++][1] = end;
                start = intervals[i][0];
                end = intervals[i][1];
            }
        }
        intervals[t][0] = start;
        intervals[t++][1] = end;
        int[][] res = new int[t][2];
        for (int i = 0;i < t;i++){
            res[i][0] = intervals[i][0];
            res[i][1] = intervals[i][1];
        }
        return res;
    }
}
```

