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

[221. Maximal Square](https://leetcode.com/problems/maximal-square/)

动态规划

[394. Decode String](https://leetcode.com/problems/decode-string/)

可以暴力解法，也可以使用stack进行稍微优雅一点的处理

[参考答案](https://leetcode.com/problems/decode-string/discuss/192903/2ms-Java-Solution-with-2-stacks)

[56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

[参考答案](https://leetcode.com/problems/merge-intervals/solution/)

[560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

动态规划并不是最优，还有更好的解决方案（比如使用`hashmap`）

[621. Task Scheduler](https://leetcode.com/problems/task-scheduler/)

排序与优先队列的使用

[3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

暴力解法性能底下

采用滑动窗口的处理方案 ```[i, j)```

[93. Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/)

这道题目非常有意思，重点在于"01"这类ip地址组成部分是不合法的。

[77. Combinations](https://leetcode.com/problems/combinations/description/)

这道题目不同于一般的回溯问题，关键在于不考虑combination中元素的顺序

[39. Combination Sum](https://leetcode.com/problems/combination-sum/description/)

这道题目与上面一样，难点都是在于不考虑combination的顺序

这样就不是常规的dfs了，可能需要修改一下认知模型

这类问题，我们统一的解决方案是：要求后加入prefix的元素必须必prefix中所有元素都要大

[40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/description/)

与上面不同点，在于candidates里面的元素只能被使用一次（但是允许重复）

### 回溯问题终极总结
1. prefix本来是有序的，如果我们的combination无序，那么我们将candidates排序即可
2. 我们一开始处理candiates是unique的，如果可以重复，那么我们必须使用set在同一层过滤

[146. LRU Cache](https://leetcode.com/problems/lru-cache/)

LRU的设计与实现：主要难点在于`key->ts`的存储与更新

[4. Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays/)




