---
title: 21. Merge Two Sorted Lists
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-15 16:09:37
subtitle:
tags:
  - LinkedList
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

 https://leetcode.com/problems/merge-two-sorted-lists/

### 题目描述

You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists in a one **sorted** list. The list should be made by splicing together the nodes of the first two lists.

Return *the head of the merged linked list*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

**Example 2:**

```
Input: list1 = [], list2 = []
Output: []
```

**Example 3:**

```
Input: list1 = [], list2 = [0]
Output: [0]
```

 

**Constraints:**

- The number of nodes in both lists is in the range `[0, 50]`.
- `-100 <= Node.val <= 100`
- Both `list1` and `list2` are sorted in **non-decreasing** order.

### 题目解析

- 简单题，细心点就可以了

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
   public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        if(list1==null) return list2;
        ListNode head = list1;
        ListNode pre = null;
        while (list1 != null && list2 != null){
            if(list1.val < list2.val){
                pre = pre ==null?list1:pre.next;
                list1 = list1.next;
            }else{
                ListNode tmp = list2.next;
                list2.next = list1;
                if(pre == null){
                    pre = list2;
                    head = list2;
                }else{
                    pre.next = list2;
                    pre = pre.next;
                }
                list2 = tmp;
                list1 = pre.next;
            }
        }
        if (list2 != null)
            pre.next = list2;
        return head;
    }
}
```

