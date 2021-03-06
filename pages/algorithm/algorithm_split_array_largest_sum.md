---
title: "Split Array Largest Sum"
tags: [algorithm, dynamic_programming, binary_search]
keywords:
summary:
sidebar: mydoc_sidebar
permalink: algorithm_split_array_largest_sum.html
folder: algorithm
toc: false
---

## Description：Hard 题
Given an array which consists of non-negative integers and an integer m, you can split the array into m non-empty continuous subarrays. Write an algorithm to minimize the largest sum among these m subarrays.

Note:
* If n is the length of array, assume the following constraints are satisfied:
  * 1 ≤ n ≤ 1000
  * 1 ≤ m ≤ min(50, n)

### Example
* Input: nums = [7,2,5,10,8], m = 2
  * Output: 18. There are four ways to split nums into two subarrays. The best way is to split it into [7,2,5] and [10,8], where the largest sum among the two subarrays is only 18.

## Solution 1：DP，速度不算快，后面的binary search的方法更快
这题所用的2个方法的解释，都可以在这里看到：https://www.youtube.com/watch?v=_k-Jb4b7b_0

dp[i][j]：数组里 从index 0 到 index i（两端都inclusive）的这些数，正好分成j个groups，那么最大的group sum 的最小值是多少

### Complexity
* Time: O(m n^2)，n是数组里有多少个数，m是groups的个数
* Space: O(m n)

### Java
代码不很长，但思路比较巧妙，也很典型，值得反复品味
```java
class Solution {
    public int splitArray(int[] nums, int m) {
        // we had a guarantee that m is <= nums.length
        if (nums == null || nums.length == 0 || m <= 0) {
            return 0;
        }
        
        int n = nums.length;
        
        int[] prefixSums = new int[n];
        prefixSums[0] = nums[0];
        for (int i = 1; i < n; i++) {
            prefixSums[i] = nums[i] + prefixSums[i - 1];
        }
        
        int[][] dp = new int[n][m + 1];
        
        // base case：只有1个group的情况
        for (int i = 0; i < n; i++) {
            dp[i][1] = prefixSums[i];
        }
        // base case：只有1个item的话，group的个数>=2的话，最小的group的和一定都是0
        
        int result = Integer.MAX_VALUE;
        
        // 从2个group开始
        for (int groups = 2; groups <= m; groups++) {
            // i从左边数第2个数开始，当前的考察结束于i，即i是最右端
            for (int i = 1; i < n; i++) {
                dp[i][groups] = Integer.MAX_VALUE;
                
                // index 0 到 index k 为一部分，这个部分里可能有1个或多个组，
                // index k+1 到 index i 为另一部分，作为一个group
                for (int k = 0; k < i; k++) {
                    // 按照这种分割方法，最大的group的sum是多少
                    int maxGroupByThisDivision = 
                        Math.max(dp[k][groups - 1], prefixSums[i] - prefixSums[k]);
                    
                    dp[i][groups] = 
                        Math.min(dp[i][groups], maxGroupByThisDivision);
                }
            }
        }
        return dp[n - 1][m];
    }
}
```

## Solution 2：高阶的binary search 技巧，速度很快

### Complexity
* Time: O(log(sum of all numbers in the array) * n)
* Space: O(1)

### Java
代码不长，思路很巧妙，还有些比较深的地方我还没有悟透，还需要反复看
```java
class Solution {
    public int splitArray(int[] nums, int m) {
        long maxElement = Integer.MIN_VALUE;
        long totalSum = 0;
        int n = nums.length;
        
        for (int num : nums) {
            totalSum += num;
            maxElement = Math.max(maxElement, num);
        }
        
        long min = maxElement;
        long max = totalSum;
        long result = totalSum;
        
        while (min <= max) {
            long mid = min + (max - min) / 2;
            long sum = 0;
            long maxSum = min;
            int count = 0;
            
            for (int num : nums) {
                if (sum + num > mid) {
                    count++;
                    sum = num;
                } else {
                    sum += num;
                }
                
                maxSum = Math.max(maxSum, sum);
            }
            count++; // 别忘了这个
            
            if (count < m) {
                // 特别注意！这里也要更新result
                // 因为我们选取的min group sum是数组里最大的element的值，
                // 但其实那些更小的elements是可能单独成为一个group的
                result = Math.min(result, maxSum);
                max = mid - 1;
            } 
            else if (count > m) {
                min = mid + 1;
            } 
            // 如果count正好等于m，也还是要继续缩小每个group的sum的上限！
            // 不能就此return！因为可能有多个上限都能实现 count == m
            else {
                result = Math.min(result, maxSum);
                max = mid - 1;
            }
        }
        return (int)result;
    }
}
```

## Reference
* [Split Array Largest Sum [LeetCode]](https://leetcode.com/problems/split-array-largest-sum/description/)

{% include links.html %}
