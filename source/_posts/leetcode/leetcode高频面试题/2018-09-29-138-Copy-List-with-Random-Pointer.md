---
title: 138. Copy List with Random Pointer
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-29 09:50:12
subtitle:
tags:
  - Linkedlist
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/copy-list-with-random-pointer/

### 题目描述

​	A linked list of length `n` is given such that each node contains an additional random pointer, which could point to any node in the list, or `null`.

Construct a [**deep copy**](https://en.wikipedia.org/wiki/Object_copying#Deep_copy) of the list. The deep copy should consist of exactly `n` **brand new** nodes, where each new node has its value set to the value of its corresponding original node. Both the `next` and `random` pointer of the new nodes should point to new nodes in the copied list such that the pointers in the original list and copied list represent the same list state. **None of the pointers in the new list should point to nodes in the original list**.

For example, if there are two nodes `X` and `Y` in the original list, where `X.random --> Y`, then for the corresponding two nodes `x` and `y` in the copied list, `x.random --> y`.

Return *the head of the copied linked list*.

The linked list is represented in the input/output as a list of `n` nodes. Each node is represented as a pair of `[val, random_index]` where:

- `val`: an integer representing `Node.val`
- `random_index`: the index of the node (range from `0` to `n-1`) that the `random` pointer points to, or `null` if it does not point to any node.

Your code will **only** be given the `head` of the original linked list.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/12/18/e1.png)

```
Input: head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
Output: [[7,null],[13,0],[11,4],[10,2],[1,0]]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2019/12/18/e2.png)

```
Input: head = [[1,1],[2,1]]
Output: [[1,1],[2,1]]
```

**Example 3:**

**![img](https://assets.leetcode.com/uploads/2019/12/18/e3.png)**

```
Input: head = [[3,null],[3,0],[3,null]]
Output: [[3,null],[3,0],[3,null]]
```

 

**Constraints:**

- `0 <= n <= 1000`
- `-104 <= Node.val <= 104`
- `Node.random` is `null` or is pointing to some node in the linked list.

### 题目解析

- 题目难度中等，看你会不会灵活处理。对已有的链表，模仿着创造出一条新的来，random也要指向对应新的节点才可以。
- 遍历3次，第一次再节点后面插入一个新的节点，第二次将对应的random指向好，第三次，将对应的新节点串联起来。 

### 代码

```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/

class Solution {
    public static  Node copyRandomList(Node head) {
        if(head == null) return head;
        Node cur = head;
        while(cur != null){
            final Node new_node = new Node(cur.val);
            new_node.next = cur.next;
            cur.next = new_node;
            cur = new_node.next;
        }
        cur = head;
        Node new_head  = null;
        Node new_last = null;
        while (cur != null){
            final Node random = cur.random;
            if (random != null){
                final Node new_random = random.next;
                cur.next.random = new_random;
            }
             cur = cur.next.next;
        }
        cur = head;
        while (cur != null){
            if (new_head == null){
                new_head = cur.next;
                new_last = cur.next;
            }
            else{
                new_last.next = cur.next;
                new_last = new_last.next;
            }
            cur.next = cur.next.next;
            cur = cur.next;
        }
        return new_head;
    }
}
```

