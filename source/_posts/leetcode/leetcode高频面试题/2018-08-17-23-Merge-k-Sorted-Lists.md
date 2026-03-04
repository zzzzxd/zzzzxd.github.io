---
title: 23. Merge k Sorted Lists
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-17 16:44:56
subtitle:
tags:
- LinkedList
- Heap
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/merge-k-sorted-lists/

### 题目描述

You are given an array of `k` linked-lists `lists`, each linked-list is sorted in ascending order.

*Merge all the linked-lists into one sorted linked-list and return it.*

 

**Example 1:**

```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```

**Example 2:**

```
Input: lists = []
Output: []
```

**Example 3:**

```
Input: lists = [[]]
Output: []
```

 

**Constraints:**

- `k == lists.length`
- `0 <= k <= 104`
- `0 <= lists[i].length <= 500`
- `-104 <= lists[i][j] <= 104`
- `lists[i]` is sorted in **ascending order**.
- The sum of `lists[i].length` will not exceed `10^4`.

### 题目解析

- 难度不大，主要考察堆，属于堆的典型题目，再配合链表，算是常考题目了

### 代码

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode head = null;
        ListNode last = null;
        PriorityQueue<ListNode> heap = new PriorityQueue<ListNode>((a,b)->a.val - b.val);
        for (int i = 0; i < lists.length; i++) {
            if(lists[i] != null){
                heap.add(lists[i]);
            }
        }
        while (!heap.isEmpty()){
            final ListNode t = heap.poll();
            if(head == null){
                head = t;
                last = t;
            }else {
                last.next = t;
                last = last.next;
            }
            if(t.next != null){
                heap.add(t.next);
            }
        }
        return head;
    }
}
```

