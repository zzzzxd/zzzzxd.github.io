---
title: 378. Kth Smallest Element in a Sorted Matrix
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-10 22:56:07
subtitle:
tags:
  - LeetCode
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/

### 题目描述

​	Given an `n x n` `matrix` where each of the rows and columns is sorted in ascending order, return *the* `kth` *smallest element in the matrix*.

Note that it is the `kth` smallest element **in the sorted order**, not the `kth` **distinct** element.

You must find a solution with a memory complexity better than `O(n2)`.

 

**Example 1:**

```
Input: matrix = [[1,5,9],[10,11,13],[12,13,15]], k = 8
Output: 13
Explanation: The elements in the matrix are [1,5,9,10,11,12,13,13,15], and the 8th smallest number is 13
```

**Example 2:**

```
Input: matrix = [[-5]], k = 1
Output: -5
```

 

**Constraints:**

- `n == matrix.length == matrix[i].length`
- `1 <= n <= 300`
- `-109 <= matrix[i][j] <= 109`
- All the rows and columns of `matrix` are **guaranteed** to be sorted in **non-decreasing order**.
- `1 <= k <= n2`

 

**Follow up:**

- Could you solve the problem with a constant memory (i.e., `O(1)` memory complexity)?
- Could you solve the problem in `O(n)` time complexity? The solution may be too advanced for an interview but you may find reading [this paper](http://www.cse.yorku.ca/~andy/pubs/X+Y.pdf) fun.

### 题目解析

- 这个题应用到了 之前矩阵二分那道题目的搜索过程，此外我们可以用二分去搜索可能存在的值，每次搜索都可以判断这个数值所在的至少的排名，可能会更前，只要每次都可以去掉一部分数据，最终就可以逼近到想要的数据。

### 代码

```java
class Solution {
 public static int kthSmallest(int[][] matrix, int k) {
        int N = matrix.length;
        int l = matrix[0][0],r = matrix[N-1][N-1];

        int real_num = 0;
        while (l <= r){
            int mid = (l+r)/2;
            final int[] res = find(matrix, mid);
            if(res[0] == k) {
                return res[1];
            }
            else if (res[0] < k){
                l = mid+1;
            }else{
                real_num = res[1];
                r = mid -1;
            }
        }
        return real_num;
    }
    public static int[] find(int[][] m,double num){

        int i = 0,j = m[0].length-1,res = 0,real_num = Integer.MIN_VALUE;
        while (i < m.length && j >= 0){
            if (m[i][j] <= num){
                real_num =Math.max(m[i][j],real_num);
                res += j+1;
                i++;
            }else{
                j--;
            }
        }
        return new int[]{res,real_num};
    }
}
```



