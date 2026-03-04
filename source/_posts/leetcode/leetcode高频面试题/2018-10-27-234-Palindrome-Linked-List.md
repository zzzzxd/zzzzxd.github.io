---
title: 234. Palindrome Linked List
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-27 23:23:49
subtitle:
tags:
  - LeetCode
  - LinkedList
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/palindrome-linked-list/

### 题目描述

​	Given the `head` of a singly linked list, return `true` if it is a palindrome.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/03/pal1linked-list.jpg)

```
Input: head = [1,2,2,1]
Output: true
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/03/03/pal2linked-list.jpg)

```
Input: head = [1,2]
Output: false
```

 

**Constraints:**

- The number of nodes in the list is in the range `[1, 105]`.
- `0 <= Node.val <= 9`

### 题目解析

- 题目就是判断链表是不是回文，你当然可以用容器存下来去判断，那这样肯定是不好的。经典的做法就是 ，找到中点或者上中点，下半段反转，两端进行遍历对比，判断完之后，给人家翻回来。

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
   public static boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) return true;
        if(head.next.next == null){
            return head.val == head.next.val;
        }
        ListNode slow = head;
        ListNode fast = head.next; //目的是上中点
        while(fast != null && fast.next != null){
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode mid = slow; //slow 为 mid or up mid
        ListNode pre = null;
        slow = mid.next;
        mid.next = null;
        while (slow != null){
            ListNode tmp = slow.next;
            slow.next = pre;
            pre = slow;
            slow = tmp;
        }
        fast = head;
        slow = pre;

        while ( slow!= null && fast.val == slow.val){
            slow = slow.next;
            fast = fast.next;
        }
        boolean res = slow == null;
        //修正
        slow = pre; //pre在最后一个节点
        pre = null;
        while (slow != null){
            slow.next = pre;
            pre =slow;
            slow = slow.next;
        }
        mid.next = pre;
        return res;
    }

}
```

