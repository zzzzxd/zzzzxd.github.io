---
title: 2. Add Two Numbers
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-04 22:09:14
subtitle:
tags:
- Linked List
categories:
- 算法
  - leetcode高频面试题
---

### 题目链接

https://leetcode.com/problems/add-two-numbers/

### 题目描述

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/02/addtwonumber1.jpg)

```
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
```

**Example 2:**

```
Input: l1 = [0], l2 = [0]
Output: [0]
```

**Example 3:**

```
Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]
```

 

**Constraints:**

- The number of nodes in each linked list is in the range `[1, 100]`.
- `0 <= Node.val <= 9`
- It is guaranteed that the list represents a number that does not have leading zeros.

### 题目解析

注意几个点

- 注意进位的处理，每次累加 记得加上进位
- 最后若仍有进位，需要单独新建一个节点来保留进位
- 为减少额外空间，可以将结果累加到l1上或者l2上而不必新建节点。

### 代码

```java
class Solution {
  public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int jie = 0;
        ListNode p1 = l1;
        ListNode p2 = l2;
        ListNode p1_last = null;;
        while (p1 != null && p2 != null){
            int left = (p1.val + p2.val + jie)%10;
            jie = (p1.val + p2.val + jie)/10;
            p1.val = left;
            p1_last = p1;
            p1 = p1.next;
            p2 = p2.next;
        }
        while (p1 != null){
            int left = (p1.val + jie)%10;
            jie = (p1.val + jie)/10;
            p1.val = left;
            p1_last = p1;
            p1 = p1.next;
        }
        while (p2 != null){
            int left = (p2.val + jie)%10;
            jie = (p2.val + jie)/10;
            p1_last.next = new ListNode(left);
            p1_last = p1_last.next;
            p2 = p2.next;
        }
        if(jie != 0){
            p1_last.next = new ListNode(jie);
        }
        return l1;
    }
}
```
