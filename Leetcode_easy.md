
- stack用法  push pop top
- queue用法  push pop front
- vector用法 erase assign
- floyd判环算法 可以高效率的检测一个链表是否有环，以及确定环的起点。使用快慢指针。[链接](https://blog.csdn.net/u012534831/article/details/74231581)
- reverse函数 `std::reverse(vec.begin(), vec.end()`
- set count() erase() find() insert() size() 
- 以及hash_set unordered_set
- hash_map用法
- 二分查找


## 7 Reverse Integer
将输入的int类型翻转，如-123 得到-321， 若超出int范围，则返回0
- int转string类型 使用 string to_string(int) 函数
- 利用stringstream完成string到long long的转换
- int atoi(string) 可以从string转到int
- 两个宏 INT_MAX INT_MIN
- 有更好的[解决方法](https://leetcode.com/problems/reverse-integer/discuss/166508/C++-beats-100)
## 9 Palindrom Number
完美数
- [巧妙方法](https://leetcode.com/problems/palindrome-number/solution/) 并不需要把int转成string
## 13 Roman to Integer
- std::map初始话两种方法 `map1[1]=2; map1.insert(pair<int, int>(11,2))`

## 20 Valid Parentheses
- stack用法  push pop top
- switch case 匹配多个值需要： `case 1: case2: case3: {....}`
- 遍历string可以 `for c in str:`

## 21 Merge Two Sorted Lists
- 做错
- [巧妙方法 14行](https://leetcode.com/problems/merge-two-sorted-lists/discuss/9714/14-line-clean-C++-Solution)

## 26 Remove Duplicates from Sorted Array
- [更好方法](https://leetcode.com/problems/remove-duplicates-from-sorted-array/solution/)
- vector `erase(itrator)` 删除一个元素   `erase(itrator-begin, itrator-end)`删除多个元素
- string `str1.find(str2, int position)` 查找字符串

## 38 Count and say
- 可以使用`while(--n)`来循环n-1次

## 58 Length of Last Word
- [更好办法](https://leetcode.com/problems/length-of-last-word/discuss/166771/4ms-Ultimate-simple-c++)

## 66 Add Binary
- for循环i，j两个参数，跳出条件记得用&&
- [更好办法](https://leetcode.com/problems/add-binary/discuss/24475/Short-code-by-c++)

## 70 Climbing Stairs
- [三种不同办法](https://leetcode.com/problems/climbing-stairs/solution/)

## 108 Convert Sorted Array to Binary Search Tree
- 不会做

## 110 Balanced Binary Tree
- 提前看了答案

## 111 Minimum Depth of Binary Tree
- 开始做错

## 121 Best Time to Buy and Sell Stock
- 有复杂度为n的[方法](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/hints/)

## 136 Single Number
- [很多奇妙的方法](https://leetcode.com/problems/single-number/description/)

## 160 Intersection of Two Linked Lists
- [很聪明的方法](https://leetcode.com/problems/intersection-of-two-linked-lists/solution/)

## 168 Excel Sheet Column Title
- 想了好久

## 169 Majority Element
- [很秀的方法](https://leetcode.com/problems/majority-element/discuss/51613/O(n)-time-O(1)-space-fastest-solution)

## 172 Factorial Trailing Zeroes

## 189 Rotate Array

## 198 House Robber
- 自己使用查表的方法，超时。[应使用dp](https://leetcode.com/problems/house-robber/discuss/167432/C++-100-run-time-O(n)-time-complexity-and-O(n)-space-complexity)

## 202 Happy Number
- 使用floyd判环算法可以将时间复杂度变成O(n)

## 203 Remove Linked List Elements
- [更简洁的方法](https://leetcode.com/problems/remove-linked-list-elements/discuss/57308/Concise-C++-solution-with-pseudo-ListHead)
- 对于链表，head可能会改变时，自己创建一个ListNode指向next会使代码更简洁

## 204 Count Primes
- [更好的查表方法](https://leetcode.com/problems/count-primes/discuss/57594/My-easy-one-round-c++-code)

## 205 Isomorphic Strings
- [使用hash表更快](https://leetcode.com/problems/isomorphic-strings/discuss/166424/Short-and-efficient-C++-solution-4-ms)

## 206 Reverse Linked List

## 234 Palindrome Linked List
- [巧妙的办法](https://leetcode.com/problems/palindrome-linked-list/discuss/167449/Java:-Simple-O(n)-time-O(1)-space-excluding-call-stack-(clever-global-variable))

## 237 Delete Node in a Linked List
- 开始没懂题目

## 278 First Bad Version
- 二分查找不会写

## 283 Move Zeroes
- [巧妙方法](https://leetcode.com/problems/move-zeroes/solution/)

## 374 Guess Number Higher or Lower
- 使用`mid=start+(end-start)/2` 而不用`mid = (start+end)/2` 是为了防止越界

## 441 Arranging Coins
- 使用二分查找更快

## 448 Find All Numbers Disappeared in an Array
- 不会O(n)解法 [解法](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/discuss/167020/Cpp-Solution-O(n))

## 453 Minimum Moves to Equal Array Elements
- 不会写   [数学思想](https://leetcode.com/problems/minimum-moves-to-equal-array-elements/discuss/93817/It-is-a-math-question)
