---
title: 121. Best Time to Buy and Sell Stock
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-23 23:08:42
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

### 题目描述

​	You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return *the maximum profit you can achieve from this transaction*. If you cannot achieve any profit, return `0`.

 

**Example 1:**

```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

**Example 2:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```

 

**Constraints:**

- `1 <= prices.length <= 105`
- `0 <= prices[i] <= 104`

### 题目解析

- 股票系列问题可是经典题目，这是股票一，买一次，卖一次求最大利润，题目转化成，对当前i位置，减去我左边的最小值，即为当前位置卖出的最大利润，遍历一趟得结果

### 代码

```java
class Solution {
    public static int maxProfit(int[] prices) {
        if (prices.length == 0) return 0;
        int res = 0,min = prices[0];
        for (int i = 1; i < prices.length; i++) {
            min = Math.min(prices[i],min);
            res = Math.max(res,prices[i]-min);
        }
        return res;
    }
}
```

