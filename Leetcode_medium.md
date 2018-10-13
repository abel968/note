- lower_bound() upper_bound()都是利用二分查找在一个排好序的数组中查找
  `lower_bound(begin, end, num)`从begin 到end-1位置查找 第一个大于等于num的数字
- trie 字典树
- 图是否有环 [方法](https://www.cnblogs.com/TenosDoIt/p/3644225.html)
- c++ STL中有heap模板，可以学习使用一下。。。


## 11 Container With Most Water
- O(n)方法没有想到  [解法](https://leetcode.com/problems/container-with-most-water/solution/)

## 47 Permutation II
- 不会。 先排序，然后手写next permutation

## 48 Rotate Image
- [更好的方法](https://leetcode.com/problems/rotate-image/discuss/177767/easy-c++-solution-beat-100)

## 49 Group Anagrams
- 不会 [方法](https://leetcode.com/problems/group-anagrams/solution/)

## 50 Pow(x, n)
- 不会O(logn)的写法

## 55 Jump Game
- 不会 [解法](https://leetcode.com/problems/jump-game/solution/)

## 56 Merge Intervals
- 不会 [解法](https://leetcode.com/problems/merge-intervals/solution/)

## 	64 Minimum Path Sum
- 用dp来做更好

## 73 Set Matrix Zeroes
- 想不出空间为O(1)的[算法](https://leetcode.com/problems/set-matrix-zeroes/solution/)

## 81 Search in Rotated Sorted Array II
- 不会

## 89 Gray Code
- 不会写 [容易理解的方法](https://leetcode.com/problems/gray-code/discuss/175438/C++-100-recursive-and-simple)
- [数学方法](https://leetcode.com/problems/gray-code/discuss/175457/C++-Iterative-Solution-Beats-100)

## 90 Subsets II
- [更简洁的递归方法](https://leetcode.com/problems/subsets-ii/discuss/176620/C++-solution-recursive-and-easy)

## 98 Validate Binary Search Tree
- [更简单的方法](https://leetcode.com/problems/validate-binary-search-tree/discuss/179057/Intuitive-Preorder-C++-Beats-100)

## 109 Convert Sorted List to Binary Search Tree
- 不会写  用快慢指针来寻找中间节点

## 127 Word Ladder
- 不会写 超时 应使用BFS，剪枝 

## 134 Gas Station
- [O(n)算法](https://leetcode.com/problems/gas-station/discuss/175196/my-c++-o(n)-solution) 没有想到

## 137 Single Number II
- 若出现两次，则需要一个bit就可以记录。`b = b xor i`
- 若出现三次，则需要两个ibt记录。`b = b xor i & ~a; a = a xor i & ~b`
- [参考](https://leetcode.com/problems/single-number-ii/discuss/167343/topic)

## 138 Copy List with Random Pointer
- 不会空间复杂度为O(1)的s[算法](https://leetcode.com/problems/copy-list-with-random-pointer/solution/)
- 其实就是把`map<Node*, Node*>`改成使用Node->next来指向Node的newe结点。最后需要恢复每个点的next

## 139 Word Break
- 自己做超时。。[算法](https://leetcode.com/problems/word-break/discuss/43814/C++-Dynamic-Programming-simple-and-fast-solution-(4ms)-with-optimization)

## 142 Linked List Cycle II
- [更好的办法](https://leetcode.com/problems/linked-list-cycle-ii/discuss/)

## 148 Sort List
- **完全不会** [方法](https://leetcode.com/problems/sort-list/discuss/46712/Bottom-to-up(not-recurring)-with-o(1)-space-complextity-and-o(nlgn)-time-complextity)

## 151 Reverse Words in a String
- 不会 时间O(n) 空间O(1)的 [方法](https://leetcode.com/problems/reverse-words-in-a-string/discuss/47840/C++-solution-in-place:-runtime-O(n)-memory-O(1))
- 先把每个单词都reverse 最后把整个string reverse

## 152 Maximum Product Subarray
- 不会 [方法](https://leetcode.com/problems/maximum-product-subarray/discuss/48230/Possibly-simplest-solution-with-O(n)-time-complexity)

## 162 Find Peak Element
- 不会 O(logn)[算法](https://leetcode.com/problems/find-peak-element/solution/)

## 166 Fraction to Recurring Decimal
- 自己写的代码太恶心。。。。 [方法](https://leetcode.com/problems/fraction-to-recurring-decimal/discuss/51109/Accepted-cpp-solution-with-explainations)

## 179 Largest Number
- 不会 [算法](https://leetcode.com/problems/largest-number/solution/)

## 187 Repeated DNA Sequences
- 不会 [算法](https://leetcode.com/problems/repeated-dna-sequences/discuss/53877/I-did-it-in-10-lines-of-C++)

## 201 Bitwise AND of Numbers Range
- 不会 [elegant 方法](https://leetcode.com/problems/bitwise-and-of-numbers-range/discuss/56746/One-line-C++-solution)

## 215 Kth Largest Element in an Array
- 直接sort时间复杂度为O(nlogn) 使用堆排序，维护大小为k的heep，时间复杂度为O(nlogk) [大致写法](https://leetcode.com/problems/kth-largest-element-in-an-array/discuss/177587/18-line-Java-Solution-with-heap)
- c++ STL中有heap模板，可以学习使用一下。。。
