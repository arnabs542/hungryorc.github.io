---
title: "Quick Sort"
tags: [algorithm, algorithm_overview, sorting]
keywords:
summary:
sidebar: mydoc_sidebar
permalink: algorithm_quick_sort.html
folder: algorithm
toc: false
---

## Overview

### Complexity
* Time: 平均情况下 O(n*logn)
  * Because the Recursion Tree has log(n) levels, and each level we need O(n) time, since we need to go through each number in the array in each level.
  * 最坏的情况下，每次pivot都选到最大值或最小值，这时的时间复杂度是 O(n^2)
* Space: 平均情况下 O(logn)
  * 因为一共有logn层 call stack，每一个 call stack 都耗费constant空间，它们都没有创造额外的 helper arrays（像 merge sort 那样）之类的空间占用者，每一层的 call stack 只定义了常数个variables
  * worst case 空间复杂度是 O(n)，仍然等于 call stack 的层数，此时会有n层call stack
  
## Implementation 1，简明版

### 特别注意！在另外一个九章的版本里，对quick sort，九章也没使用 left+1<=right !

这种写法的特点：
* 每次跑一遍 `private void quickSort(int[] array, int start, int end)` 以后，left index 在 right index 的右边：
  ```
  ... *  *  *  *  right  left *  *  *  *  ...
  ```
  * 但是此时并不能保证left左边的数都小于等于它！也不能保证right右边的数都大于等于它！
  * 只能保证 **right左边的数包括right，小于等于 left右边的数包括left**。就是说，只是两个区间之间的大小关系！而此时left和right位置上的2个数的值未必是划分大小的分界值！
  
```java
public class Solution {
    public int[] quickSort(int[] array) {
        if (array == null || array.length <= 1) {
            return array;
        }
        
        quickSort(array, 0, array.length - 1);
        return array;
    }
    
    private void quickSort(int[] array, int start, int end) {
        if (start >= end) {
            return;
        }
        
        int left = start, right = end;
        int pivot = array[start + (end - start) / 2];

        while (left <= right) {
            if (array[left] < pivot) {
                left ++;
            } else if (array[right] > pivot) {
                right --;
            } else {
                swap(array, left++, right--);
            }
        }
        // 特别注意，while 循环结束以后，left 和 right 的关系是 right + 1 = left 
        // 即 right 在 left 的左边一位
        <=== 是么 ？？？ 有没有可能他们两中间间隔一位 ？？？因为在left==right的时候还可能进行left++且right--的操作！！！
        
        
        // 这里没有判断左右index的大小关系，下一个recursion里的头部会做
        quickSort(array, start, right);
        quickSort(array, left, end);
    }
    
    private void swap(int[] array, int i, int j) {
        int temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

## Implementation 2，经典版

### 比上面的写法繁琐，最重要的是每次partition能把分界值放到分界点上！靠最后那一下swap！

这种写法的特点：
* 每次跑一遍 `private int partition(int[] array, int start, int end)` 以后：
  * 这个函数返回的int值正好就是分界点的坐标！而且分界点此时的值能保证就是分界值！分界值的意思是说，它左边的所有数都保证大于等于它！它右边的所有数都保证小于等于它！这是这个写法比上一个写法强力的地方。做到这一点，是靠最后的那个 `swap(array, left, end)` 来保证的！
  * 两种quick sort的写法，最后sort的结果是一样的。所以如果不是特别需要这一点，也不用非要用这种写法。
  
```java
public class Solution {
    public int[] quickSort(int[] array) {
        if (array == null || array.length <= 1) {
            return array;
        }
        
        quickSort(array, 0, array.length - 1);
        return array;
    }
    
    // Overload quickSort method
    public void quickSort(int[] array, int left, int right) {
        if (left >= right) {
            return;
        }
        
        // the following method complete 2 tasks:
        // 1. get and return the pivotIndex
        // 2. partition the array using this pivot
        int pivotIndex = partition(array, left, right);

        // the pivot is already at the position where it should be, so 
        // when we do the recursion on the two partitions, pivot should NOT be included in any of them
        quickSort(array, left, pivotIndex - 1);
        quickSort(array, pivotIndex + 1, right);
    }

    private int partition(int[] array, int start, int end) {
        int pivotIndex = findPivotIndex(start, end);
        int pivot = array[pivotIndex];
        
        // swap the pivot element to the rightmost position
        swap(array, pivotIndex, end);
        
        int left = start;
        int right = end - 1;
        
        while (left <= right) {
            if (array[left] < pivot) {
                left ++;
            } else if (array[right] > pivot) {
                right --;
            } else {
                swap(array, left++, right--);
            }
        }
        // 这个while loop结束时，一定是：rightIndex 在 leftIndex 的左边一位 ！
        
        <=== 是么 ？？？ 有没有可能他们两中间间隔一位 ？？？因为在left==right的时候还可能进行left++且right--的操作！！！
        
        // 特别注意下面的处理方式 ！！！
        // ------------------------------------------------------------------------------------------------
        // 如果一开始pivot被移动到最右边，那么现在pivot要和leftIndex换，
        // 因为此时leftIndex指向的数一定 >= pivot ！！！
        // 如果一开始pivot被移动到最左边，那么现在pivot要和rightIndex换，
        // 因为此时rightIndex指向的数一定 <= pivot ！！！
        swap(array, left, end);
        
        // 这么移动之后，pivot 一定就位于我们接下来要return 的index的位置上，
        // 即这个位置的数一定 == pivot，而非含糊地 >= pivot 或 <= pivot ！！！
        // 注意了 ！！！
        // 之所以这里非要等于不可，不能有一丝的含糊，是因为再往后的 quick sort recursion里，
        // 前半段的quick sort 将结束于 pivot index - 1，
        // 后半段的quick sort 将开始于 pivot index + 1，
        // 所以 pivot index 上必须必须是等于 pivot 值的 ！！！因为以后再也不会碰这个 index 上的数了 ！！！
        // 至于 pivot index 之前或者之后的数，目前来说，完全可能含有n个等于pivot的数，这些都无所谓，
        // 但是在pivot位置上必须就是pivot值！！！然后下一步才有继续的基础！！！
        
        // 上面如果是 swap 了 right 和 start，这里就要 return right ！！！
        // 上面如果是 swap 了 left 和 end，这里就要 return left ！！！
        return left;
        // ------------------------------------------------------------------------------------------------
    }
    
    // random in the range of [left, right], inclusive in both ends
    // Math.random() 得出的是：[0, 1)
    private int findPivotIndex(int left, int right) {
        return left + (int)(Math.random() * (right - left + 1));
    }

    private void swap(int[] array, int left, int right) {
        int temp = array[left];
        array[left] = array[right];
        array[right] = temp;
    }
}
```

## Reference
* [Quick Sort [LaiCode]](https://app.laicode.io/app/problem/10)

{% include links.html %}
