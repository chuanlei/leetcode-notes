[81. Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)
```java
class Solution {
    public void setZeroes(int[][] matrix) {

    }
}
```

如何使用O(1)的空间完成任务？诀窍在于使用矩阵的第一行、第一列存储信息

[103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)

注意stack与queue的区别

[274. H-Index](https://leetcode.com/problems/h-index/)

参考：https://leetcode.com/problems/h-index/discuss/70768/Java-bucket-sort-O(n)-solution-with-detail-explanation

[275. H-Index II](https://leetcode.com/problems/h-index-ii/)

与前一题不同的地方是给定的citation是已经排完序了，要求时间复杂度为`ln(n)`

这里的问题非常经典，值得好好学习。

找到第一个满足以下条件的

```
citations[index] >= length(citations) - index. 
```

[540. Single Element in a Sorted Array (Medium)](https://leetcode.com/problems/single-element-in-a-sorted-array/description/)

与上题有类似之处，可以参考学习（二分查找的原理）

[121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

解决方案为[Kadane's Algorithm](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/discuss/39038/Kadane's-Algorithm-Since-no-one-has-mentioned-about-this-so-far-%3A)

[21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

尾插法性能较低，有什么其他方案吗？

递归倒也是不错的方案(本质上是头插法）

[7. Reverse Integer](https://leetcode.com/problems/reverse-integer/)

注意整数溢出的问题

[5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)

边界情况的处理非常有意思

居然不是使用暴力解法，而是使用动态规划，那就更有意思了

果然有可以复用的子结构

[31. Next Permutation](https://leetcode.com/problems/next-permutation/)

未完待续
