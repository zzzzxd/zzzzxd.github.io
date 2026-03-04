---
title: 149. Max Points on a Line
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-03 10:42:16
subtitle:
tags:
  - Math
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/max-points-on-a-line/

### 题目描述

​	Given an array of `points` where `points[i] = [xi, yi]` represents a point on the **X-Y** plane, return *the maximum number of points that lie on the same straight line*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/25/plane1.jpg)

```
Input: points = [[1,1],[2,2],[3,3]]
Output: 3
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/02/25/plane2.jpg)

```
Input: points = [[1,1],[3,2],[5,3],[4,1],[2,3],[1,4]]
Output: 4
```

 

**Constraints:**

- `1 <= points.length <= 300`
- `points[i].length == 2`
- `-104 <= xi, yi <= 104`
- All the `points` are **unique**.

### 题目解析

- 这个看规模 长度最大 300，300个点，就for循环遍历一点问题没有
- 分析题目，其实就查 和你在一条直线上的点，用两个点计算出表达式，计算后面点是不是在表达式上，统计个数，拿出最大值即可。
- 注意！做差和0比的时候，double 这种浮点数容易出现精度问题，导致结果不是0，拿精度比。

### 代码

```java
class Solution {
  public static int maxPoints(int[][] points) {
        double eps = 1e-8;
        int N = points.length;
        if (N == 1) return 1;
        int res = 2;

        for (int p1 = 0; p1 < N - 1; p1++) {
            for (int t = p1 + 1; t < N; t++) {
                //计算斜率及直线表达式
                double k = points[t][0] - points[p1][0] == 0 ? Integer.MAX_VALUE : (double)(points[t][1] - points[p1][1]) / (points[t][0] - points[p1][0]);
                double b = 0;
                if (k != Integer.MAX_VALUE) {
                    b = points[p1][1] - k * points[p1][0];
                }
                int tmp_max = 2;
                int p2 = t + 1;
                // 扫描共线点
                if (k == Integer.MAX_VALUE) {
                    while (p2 < N) {
                        if (points[p2][0] == points[p1][0]) {
                            tmp_max++;
                            res = Math.max(tmp_max, res);
                        }
                        p2++;
                    }

                } else {
                    while (p2 < N) {
                        if (Math.abs(points[p2][1]-(k * points[p2][0] + b)) < eps) {
                            tmp_max++;
                            res = Math.max(tmp_max, res);
                        }
                        p2++;
                    }
                }
            }
        }
        return res;
    }

}
```



