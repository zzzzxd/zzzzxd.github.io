---
title: 19. Remove Nth Node From End of List
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-15 12:47:14
subtitle:
tags:
  - LinkedList
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/remove-nth-node-from-end-of-list/

### 题目描述

​	Given the `head` of a linked list, remove the `nth` node from the end of the list and return its head.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

```
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]
```

**Example 2:**

```
Input: head = [1], n = 1
Output: []
```

**Example 3:**

```
Input: head = [1,2], n = 1
Output: [1]
```

 

**Constraints:**

- The number of nodes in the list is `sz`.
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

### 题目解析

- 简单题，考察数据结构基本功，写的时候耐心点，细节点即可。
- 小技巧，保留一个pre和cur遍历指针相隔K个，这样等cur==null时，pre指向的就是要的元素。

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
   public static ListNode removeNthFromEnd(ListNode head, int n) {
          if(head == null) return null;
          ListNode cur = head,pre = null;
          int step = 0;
          while (cur != null){
              if(step >= n){
                  pre = pre == null ? head : pre.next;
              }
              cur = cur.next;
              step++;
          }
          if(pre == null){
              return head.next;
          }else{
              pre.next = pre.next.next;
              return head;
          }

    }
}
```

