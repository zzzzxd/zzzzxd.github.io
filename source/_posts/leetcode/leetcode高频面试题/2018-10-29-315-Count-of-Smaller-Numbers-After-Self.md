---
title: 315. Count of Smaller Numbers After Self
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-29 19:54:53
subtitle:
tags:
  - LeetCode
  - Merge Sort
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/count-of-smaller-numbers-after-self/

### 题目描述

​	You are given an integer array `nums` and you have to return a new `counts` array. The `counts` array has the property where `counts[i]` is the number of smaller elements to the right of `nums[i]`.

 

**Example 1:**

```
Input: nums = [5,2,6,1]
Output: [2,1,1,0]
Explanation:
To the right of 5 there are 2 smaller elements (2 and 1).
To the right of 2 there is only 1 smaller element (1).
To the right of 6 there is 1 smaller element (1).
To the right of 1 there is 0 smaller element.
```

**Example 2:**

```
Input: nums = [-1]
Output: [0]
```

**Example 3:**

```
Input: nums = [-1,-1]
Output: [0,0]
```

 

**Constraints:**

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`

### 题目解析

- 愚蠢了，没反应过来，一开始看成了，在他后面是第几小元素，实际人家问的是后面有几个比它小的，典型的逆序对，用归并排序去解决 。

### 代码

```java
class Solution {
     private static class NodeInfo{
        int value;
        int index;

        public NodeInfo(int value, int index) {
            this.value = value;
            this.index = index;
        }

        @Override
        public String toString() {
            return "NodeInfo{" +
                    "value=" + value +
                    ", index=" + index +
                    '}';
        }
    }
  public static List<Integer> countSmaller(int[] nums){
        NodeInfo[] nodes = new NodeInfo[nums.length];
        for (int i = 0; i < nums.length; i++) {
            nodes[i] = new NodeInfo(nums[i],i);
        }
        final ArrayList<Integer> list = new ArrayList<>();
        for (int i = 0;i < nums.length;i++) list.add(0);
        if (list.size() < 1) return list;
        process(nodes,0,nodes.length-1,list);
        return list;
    }
    public static void process(NodeInfo[] num,int l,int r,List<Integer> list){
        if(l >= r) {
            return;
        }
        int mid = l + (r-l)/2;
        process(num,l,mid,list);
        process(num,mid+1,r,list);
        merge(num,l,r,list);
    }
    public static void merge(NodeInfo[] num, int l , int r, List<Integer> list){
        final NodeInfo[] t = new NodeInfo[r - l + 1];
        int c = 0;
        int mid = l + (r-l)/2;
        int p1 = l,p2 = mid+1;
        while (p1 <= mid && p2 <= r){
            if(num[p2].value >= num[p1].value){
                list.set(num[p1].index,list.get(num[p1].index)+p2-mid-1);
                p1++;
            }else
                p2++;
        }
        while (p1 <= mid){
            list.set(num[p1].index,list.get(num[p1].index)+r-mid);
            p1++;
        }
        p1 = l;
        p2 = mid+1;
        while (p1 <= mid && p2 <= r){
            if(num[p1].value < num[p2].value){
                t[c++] = num[p1++];
            }else{
                t[c++] = num[p2++];
            }
        }
        while (p1 <= mid ) t[c++] = num[p1++];
        while (p2 <= r )   t[c++] = num[p2++];
        c = 0;
        for (int i = l;i <= r;i++){
            num[i] = t[c++];
        }
    }
}
```





