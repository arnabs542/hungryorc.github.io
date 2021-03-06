---
title: "Build a Binary Tree by an Inorder Array, and the Tree Must Be a Min Heap"
tags: [algorithm]
keywords: apple
summary:
sidebar: mydoc_sidebar
permalink: algorithm_buildTree_fromInorderArray_resultAsMinHeap.html
folder: algorithm
toc: false
---

## Description
Build a Binary Tree from the given array:
1. The inorder traversal of the tree has to be same as the array
2. The tree has to be a min heap (the leave has to be larger than parents)

### Example
* Input: array: {7, 3, 6, 1, 5, 4}
  * Output: 
    ```
           1
        /     \
       3       4
     /   \    /
    7     6  5
    ```

## Solution：这题有笛卡尔树的基因在里面。我找了好多人的网文才算懂了
下面这个做法有我的特色。网文都说笛卡尔树要维持一个右链，即root和root的所有直系右子孙。但我想到的办法是维护一个左链，没想到如何维护右链。不过结果应该是ok的

就以上面的array作为例子，一步一步分解如下。我们需要 建一个树，我们还需要维持一个 stack，这是一个从栈底到栈顶单调递减的 单调栈。最后可以看出，数组里的每个元素即树里的每个node，最多只会出栈一次且入栈一次，
所以最终的总时间是 O(n)

注意，我们这题除了min heap的要求，还有一个要符合in order array的既定顺序。所以可以看到，下面的算法里，新来了一个数以后，都不往当前root的左子树深翻，只和当前stack顶部的元素对比，如果要往下翻则往当前root的右子树翻，这样才能保证新来的永远子in order 的右边（更后）的位置

* 先把左边第一个数 7 设为root node，同时把它放到栈里
  * Stack: `[ 7`，用 `[` 表示 栈底
  * Tree:
    ```
     7
    ```
  * 为了方便写代码，我们可以一开始就设置一个 虚根，因为它必须比所有tree里的nodes都要小，而且都要更靠前（在 in order array里），所以 虚根永远是当前实根的 左爸爸。我们把虚根的value设为 负无穷，这自是有所图，哦也
    ```
    -Infinite  <=== 永远稳坐钓鱼台的虚根
         \
          7  <=== 当前的实际root
    ```
* 原数组里的第二个元素是 3。3和当前的栈顶元素7比较，3 < 7，所以3必须是7的爸爸。同时3又比7后到，所以3必须是7的右爸爸
  * 对于栈，我们说了这是个从底到顶递减的单调栈，3比7小，所以得以留下。新栈为：`[ 7 3`
  * 对于树，7原来已经有个爹了（我们设了虚根以后，所有实node都永远有爹，这样处理起来就简化了一点，不需要再考虑当前的node是否事儿tree的root），7的原爹的新的右子将是3。然后3的左子是7：
    ```
    -Inf
       \
        3
       /
      7
    ```
* 再来一个6，6比栈顶的3大。所以我们的单调下降栈是不能留6的。对于树，6比3后且大，所以6必须是3的右子。
  * **如果3没有右子，则直接把6接到3的右子处**
  * **如果3有右子且它也小于6，则把当前node从3移动到3的右子，继续用6来考察它**
  * **如果3有右子且它大于6，则6成为3的右子且3的原右子成为6的左子，这是因为3的原右子树上的nodes都必须比6早出现在in order 里**
  * Stack: `[7 3`
  * Tree:
    ```
    -Inf
       \
        3
      /   \
     7     6
    ```
* 再来一个1，1比栈顶的3小。所以我们的单调下降栈要留下这个1。对于树，1比3后且小，所以1必须是3的右父
  * Stack: `[7 3 1`
  * Tree:
    ```
      -Inf
         \
          1
         /
        3
      /   \
     7     6
    ```
* 再来一个5，5比栈顶的1大。所以我们的单调下降栈不留5。对于树，5比1后且大，所以5必须是1的右子。1正好没有右子，所以5直接接上。按照上面写的3种情况（当新来的比栈顶元素要大时 的3种情况）来做就行
  * Stack: `[7 3 1`
  * Tree:
    ```
      -Inf
         \
          1
         /  \
        3    5
      /   \
     7     6
    ```
* 再来一个4
  * Stack: `[7 3 1`
  * Tree:
    ```
      -Inf                    -Inf
         \                       \ 
          1                       1
         /  \                    /  \
        3    5  <=== 4          3    4
      /   \                   /  \  /
     7     6                 7   6  5
    ```

### Complexity
* Time: O(n)
* Space: O(n)，stack size

### Java
```java

```

## Reference
这是apple的电面题。网上没看到这题

{% include links.html %}
