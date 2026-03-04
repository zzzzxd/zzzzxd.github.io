---
title: 295. Find Median from Data Stream
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-28 19:06:01
subtitle:
tags:
  - LeetCode
  - Heap
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/find-median-from-data-stream/

### 题目描述

​	The **median** is the middle value in an ordered integer list. If the size of the list is even, there is no middle value and the median is the mean of the two middle values.

- For example, for `arr = [2,3,4]`, the median is `3`.
- For example, for `arr = [2,3]`, the median is `(2 + 3) / 2 = 2.5`.

Implement the MedianFinder class:

- `MedianFinder()` initializes the `MedianFinder` object.
- `void addNum(int num)` adds the integer `num` from the data stream to the data structure.
- `double findMedian()` returns the median of all elements so far. Answers within `10-5` of the actual answer will be accepted.

 

**Example 1:**

```
Input
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
Output
[null, null, null, 1.5, null, 2.0]

Explanation
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // return 1.5 (i.e., (1 + 2) / 2)
medianFinder.addNum(3);    // arr[1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

 

**Constraints:**

- `-105 <= num <= 105`
- There will be at least one element in the data structure before calling `findMedian`.
- At most `5 * 104` calls will be made to `addNum` and `findMedian`.

### 题目解析

- 要求没输入一个数输出此前所有元素中的中位数，如果是偶数个元素，输出中间两个元素的平均值。
- 可以用BST，存下元素以及元素的个数、左子树的个数、右子树的个数，这样我们每次log级别的插入元素时候，是可以顺表数出来中位数的，But，BST最差会成链，所以还得改成AVL这种动态平衡的，那这个题就比较麻烦了，实际上我们只要最中间的两个数。
- 可以用堆，一个最大堆，保留前一半的数，一个最小堆保留后面的数。

### 代码

```java
class MedianFinder {

    public MedianFinder() {
        
    }
    
   public  Queue<Integer> pre = new PriorityQueue<Integer>((a,b)->b-a);
    public Queue<Integer> post = new PriorityQueue<Integer>();
    public  void addNum(int num) {
        if(pre.isEmpty()){
            pre.add(num);
            return;
        }
        if (num > pre.peek()){
            post.add(num);
            if (post.size() - pre.size() > 1)
                pre.add(post.poll());
        }else{
            pre.add(num);
            if (pre.size() - post.size() > 1)
                post.add(pre.poll());
        }
    }

    public  double findMedian() {
        if (pre.size() == post.size()) return (pre.peek() + post.peek())*1.0/2;
        else{
            return pre.size() > post.size() ? pre.peek() : post.peek();
        }
    }
}

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```

