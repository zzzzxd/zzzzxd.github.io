---
title: 328. Odd Even Linked List
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-06 22:20:39
subtitle:
tags:
  -	LinkedList
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/odd-even-linked-list/

### 题目描述

​	Given the `head` of a singly linked list, group all the nodes with odd indices together followed by the nodes with even indices, and return *the reordered list*.

The **first** node is considered **odd**, and the **second** node is **even**, and so on.

Note that the relative order inside both the even and odd groups should remain as it was in the input.

You must solve the problem in `O(1)` extra space complexity and `O(n)` time complexity.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/10/oddeven-linked-list.jpg)

```
Input: head = [1,2,3,4,5]
Output: [1,3,5,2,4]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/03/10/oddeven2-linked-list.jpg)

```
Input: head = [2,1,3,5,6,4,7]
Output: [2,3,6,7,1,5,4]
```

 

**Constraints:**

- The number of nodes in the linked list is in the range `[0, 104]`.
- `-106 <= Node.val <= 106`

### 题目解析

- 就是遍历一趟把奇数的节点和偶数的节点分成两个链表

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
   public ListNode oddEvenList(ListNode head) {
        if (head==null||head.next == null || head.next.next==null) return head;
        ListNode odd = head;
        ListNode even_head = head.next;
        ListNode even = head.next;
        ListNode cur = even.next;
        int step = 3;
        while (cur != null){
            if((step&1)==1){
                odd.next = cur;
                odd = odd.next;
            }else{
                even.next = cur;
                even = even.next;
            }
            step++;
            cur= cur.next;
        }
       odd.next = even_head;
        even.next = null;
        return head;
    }
}
```



