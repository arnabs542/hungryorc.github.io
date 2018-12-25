---
title: "Paint House I"
tags: [algorithm, dynamic_programming]
keywords:
summary:
sidebar: mydoc_sidebar
permalink: algorithm_paint_house.html
folder: algorithm
toc: false
---

## Description
There are a row of n houses, each house can be painted with one of the three colors: red, blue or green. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a `n x 3` cost matrix. For example, `costs[0][0]` is the cost of painting house 0 with color red; `costs[1][2]` is the cost of painting house 1 with color green, and so on... Find the minimum cost to paint all houses.

Note: All costs are positive integers.

### Example
* Input: [[17,2,17],[16,16,5],[14,3,19]]
  * Output: 10. Paint house 0 into blue, paint house 1 into green, paint house 2 into blue. Minimum cost: 2 + 5 + 3 = 10.

## Solution 1: DP
挺简明的逻辑。把3种颜色分开处理。dp[i][j] 表示：index 为 i 的房子涂了编号为j 的颜色 的前提下，从index 0 到 index i (inclusive at both ends) 的所有房子的总cost的最小值

### Complexity
* Time: O(n * m * (m - 1)) = O(n * m^2)，其中 n 是房子的个数，m 是颜色的个数
* Space: O(n * m)

### Java
```java
class Solution {
    public int minCost(int[][] costs) {
        if (costs == null || costs.length == 0) {
            return 0;
        }
        
        int n = costs.length; // number of houses
        int m = 3; // number of colors
        
        int[][] dp = new int[n][m];
        
        // base case
        for (int i = 0; i < m; i++) {
            dp[0][i] = costs[0][i];
        }
        
        for (int i = 1; i < n; i++) {
            dp[i][0] = Math.min(dp[i - 1][1], dp[i - 1][2]) + costs[i][0];
            dp[i][1] = Math.min(dp[i - 1][0], dp[i - 1][2]) + costs[i][1];
            dp[i][2] = Math.min(dp[i - 1][1], dp[i - 1][0]) + costs[i][2];
        }
        
        return Math.min(dp[n - 1][0], Math.min(dp[n - 1][1], dp[n - 1][2]));
    }
}
```

## Solution 1.1: 优化Solution1的空间，dp矩阵优化到 两行（似乎无法优化到一行），但会导致时间略微增加
Space O(n * m) 可以优化到 O(2 * m) 即 O(m)，因为每个dp[i][...] 只看 dp[i - 1][...]

这题不太能用滚动数组直接优化到一行dp数组，只好优化到两行的dp矩阵，因为dp矩阵在第一维一定以后，第二维之间的m个数是互相dependent的，所以不便于用滚动数组。用滚动数组的话，各个元素会互相叠加，导致错误

### Complexity
* Time: 和 Solution 1 一样，O(n * m^2)，其中 n 是房子的个数，m 是颜色的个数
* Space: O(m)

### Java
```java
class Solution {
    public int minCost(int[][] costs) {
        if (costs == null || costs.length == 0) {
            return 0;
        }
        
        int n = costs.length; // number of houses
        int m = 3; // number of colors
        
        // 第一维从n变成2，
        // 其中dp[0][m] 代表 prev house，dp[1][m] 代表cur house
        int[][] dp = new int[2][m];
        
        // base case
        for (int i = 0; i < m; i++) {
            dp[0][i] = costs[0][i];
        }
        
        for (int i = 1; i < n; i++) {
            dp[1][0] = Math.min(dp[0][1], dp[0][2]) + costs[i][0];
            dp[1][1] = Math.min(dp[0][0], dp[0][2]) + costs[i][1];
            dp[1][2] = Math.min(dp[0][1], dp[0][0]) + costs[i][2];
            
            // 节约了空间，但多了下面这三步，导致多用了时间。虽然没造成时间上的量级增加
            for (int i = 0; i < m; i++) {
                dp[0][i] = dp[1][i];
            }
        }
        
        return Math.min(dp[0][0], Math.min(dp[0][1], dp[0][2]));
    }
}
```

## Solution 2: 大幅优化DP时间消耗



## Reference
* [Paint House [LeetCode]](https://leetcode.com/problems/paint-house/description/)

{% include links.html %}