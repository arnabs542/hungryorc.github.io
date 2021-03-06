---
title: "Number of Subarrays whose Sum is Equals k"
tags: [algorithm]
keywords:
summary:
sidebar: mydoc_sidebar
permalink: algorithm_subarray_sum_equals_k.html
folder: algorithm
toc: false
---

## Description
Given an array of integers and an integer k, you need to find the total number of continuous subarrays whose sum equals to k.
* The length of the array is in range [1, 20,000].
* The range of numbers in the array is [-1000, 1000] and the range of the integer k is [-1e7, 1e7].

### Example
* Input: nums = [1,1,1], k = 2
  * Output: 2
* Input: nums = [-1,-1,1], k = 0
  * Output: 1
* Input: nums = [-1,-1,1], k = -1
  * Output: 2

## Solution
有点像 Two Sum 的思路。另一个关键点是想到用 prefix sum。总而言之：把所有prefix sum都求出来，然后看对于每个prefix sum，有多少个 **在他之前的** prefix sum 等于 他减去k

注意:
* 做prefix sum时，别忘了从index=0开始的prefix sum，这样就需要在map里放“index=-1时的prefix sum”，即 0
* 只有后面的prefix sum才能减去前面的prefix sum！反过来是不行的
  * 所以如果把所有的prefix sum都放在一个map里以后再处理，则难以解决上面两个问题
* 所以，**这一题的解法最大的trick是：从左到右在走的过程中就做了所有事，见下面的代码**

### Complexity
* Time: O(n)
* Space: O(n), the map

### Java
```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        
        int n = nums.length;
        int result = 0;
        
        // 这一题不需要整个prefix sum array，只需要一个当前的prefix sum就行，
        // 因为后面有个map记录所有的prefix sum及其出现次数了。
        int prefixSum = 0; // 这个0是记录不含任何数时的prefix sum，即左边第一个元素的左边
        
        // <prefixSum, number of occurrences of this prefixSum>
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1); // 这里就是放前面那个prefix sum
        
        for (int i = 0; i < n; i++) {
            prefixSum += nums[i]; 
            
            result += map.getOrDefault(prefixSum - k, 0);
            
            int occurrences = map.getOrDefault(prefixSum, 0);
            map.put(prefixSum, occurrences + 1);
        }

        return result;
    }
}
```

## Reference
* [Subarray Sum Equals K [LeetCode]](https://leetcode.com/problems/subarray-sum-equals-k/description/)

{% include links.html %}
