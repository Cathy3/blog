---
title: 递归(python)
date: 2019-03-02
categories: 
	- Algorithm
tags:  
        - python
        - LeetCode
        - 数据结构
        - 递归
---
在函数的定义中对这个函数自身的调用，就是递归。

递归结构中，递归的部分必须比原来的整体简单，才有可能到达某种终结点(出口)。而且必须存在非递归的基本结构构成的部分，否则会无限递归。

学习目标：

-   编程实现斐波那契数列求值 f(n)=f(n-1)+f(n-2)
-   编程实现求阶乘 n!
-   编程实现一组数据集合的全排列
-   LeetCode 70. 爬楼梯

以上问题都可以用递归来解决。

<!-- more -->

# 斐波那契数列求值 f(n)=f(n-1)+f(n-2)
斐波那契数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。

[0,1,1,2,3,5,8,13,...]  给定`n`,计算 `f(N)`

对应LeetCode习题：[509. Fibonacci Number](https://leetcode-cn.com/problems/fibonacci-number/)

## 方法
```python
class Solution:
    def fib(self, N: int) -> int:
        if N <= 1:
            return N
        return self.fib(N-1) + self.fib(N-2)
```

# 求阶乘 n!
```python
def factorial(n):
    if n == 0 or n == 1:
        return 1
    else:
        return (n*factorial(n-1))

a = factorial(3)
print(a)
```
输出：6

# LeetCode 46. 全排列
[Permutations](https://leetcode-cn.com/problems/permutations/)

给定一个**没有重复**数字的序列，返回其所有可能的全排列。

**示例**

> 输入: [1,2,3]

> 输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]


## 方法
每次选择一个数出来，然后把剩下的数，再选择一个出来，依次类推，选到头，就回溯到上一层。
```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        res = []
        
        def dfs(nums = nums, path = []):
            if not nums:
                res.append(path)
            for i in range(len(nums)):
                dfs(nums[:i]+nums[i+1:], path+[nums[i]])
        dfs()
        return res
```
# LeetCode 47. 全排列 II
[Permutations II](https://leetcode-cn.com/problems/permutations-ii/)

给定一个可包含重复数字的序列，返回所有不重复的全排列。

**示例:**

> 输入: [1,1,2]

> 输出:
[
  [1, 1, 2], 
  [1, 2, 1], 
  [2, 1, 1] 
]


## 方法
相比上一题，多了排序和内部的重复判断。
```python
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        res = []
        
        def dfs(nums = nums, path=[]):
            if not nums:
                res.append(path)
            for i in range(len(nums)):
                if i>0 and nums[i] == nums[i-1]:
                    continue
                dfs(nums[:i]+nums[i+1:], path+[nums[i]])
        dfs()
        return res
```

# LeetCode 70. 爬楼梯
[Climbing Stairs](https://leetcode-cn.com/problems/climbing-stairs/)


假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。


**示例 ：**
```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```

## 方法：~~递归~~ 
当有n个台阶时，可供选择的走法可以分两类：1，先跨一阶再跨完剩下n-1阶；2，先跨2阶再跨完剩下n-2阶。所以n阶的不同走法的数目是n-1阶和n-2阶的走法数的和。 这和斐波那契数列的规律相同，可以用这个思路。
```python
class Solution:
    def climbStairs(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n<=2:
            return n 
        return self.climbStairs(n-1) + self.climbStairs(n-2)
```
这个方法运行正常，但是因为超出时间限制，未能通过。
## 方法：动态规划
动态规划来记录历史数据。
```python
class Solution:
    def climbStairs(self, n):
        """
        :type n: int
        :rtype: int
        """
        prev, current = 0, 1
        for i in range(n):
            prev, current = current, prev + current
        return current
```