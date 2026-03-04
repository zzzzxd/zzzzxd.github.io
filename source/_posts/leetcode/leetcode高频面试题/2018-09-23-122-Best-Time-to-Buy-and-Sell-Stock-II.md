---
title: 122. Best Time to Buy and Sell Stock II
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-23 23:46:48
subtitle:
tags:
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/

### 题目描述

​	You are given an integer array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

On each day, you may decide to buy and/or sell the stock. You can only hold **at most one** share of the stock at any time. However, you can buy it then immediately sell it on the **same day**.

Find and return *the **maximum** profit you can achieve*.

 

**Example 1:**

```
Input: prices = [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
Total profit is 4 + 3 = 7.
```

**Example 2:**

```
Input: prices = [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Total profit is 4.
```

**Example 3:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: There is no way to make a positive profit, so we never buy the stock to achieve the maximum profit of 0.
```

 

**Constraints:**

- `1 <= prices.length <= 3 * 104`
- `0 <= prices[i] <= 104`

### 题目解析	

- 股票系列问题 第二问，是说 同时可以持有一份股票，可以当天买，当天卖，交易次数不限，求最大利润，问题其实可以转换成，再上坡，下坡中，求所有上坡的和

### 代码

```java
class Solution {
    public static int maxProfit(int[] prices) {
        int res = 0;
        for(int i = 0;i < prices.length-1;i++){
            res  += Math.max(0,prices[i+1]-prices[i]);
        }
        return res;
    }
}
```

