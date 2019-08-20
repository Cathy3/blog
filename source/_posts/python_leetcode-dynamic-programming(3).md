---
title: python解leetcode动态规划(2)
date: 2019-08-20
categories: 
	    -   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   动态规划
---
动态规划主要方法是画网格⭐。

1.  递归 + 记忆化 ——> 递推（动态规划）
2.  状态的定义：数组 opt[n], dp[n], fib[n] 
3.  状态转移方程：opt[n] = best_of(opt[n-1], opt[n-2], ...) 从前面的n-1个值得到最优的第n个值
4.  最优子结构

**学习内容：**  0-1 背包问题🚩

-   ❓自我实现
-   ❓Palindrome Partitioning II(132)
-   139. 单词拆分
-   416. 分割等和子集
-   [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)
-   ❓377
-   ❓474
-   ❓140
-   ❓494

    
<!-- more -->

# 139. 单词拆分
给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

-   拆分时可以重复使用字典中的单词。
-   你可以假设字典中没有重复的单词。

示例 1：
```
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
```
示例 2：
```
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。
```
示例 3：
```
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

## 方法
S能拆成功的话，说明前一部分和后一部分能拆开。每一部分也分别可能分解为可拆开的一小部分。

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        dp = [False] * (len(s)+1)
        dp[0] = True
        for i in range(1,len(s)+1):
            for j in range(i):
                if dp[j] and s[j:i] in wordDict:
                    dp[i] = True
        return dp.pop()
```

# 416. 分割等和子集
给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

注意:

-   每个数组中的元素不会超过 100
-   数组的大小不会超过 200

示例 1:
```
输入: [1, 5, 11, 5]
输出: true
解释: 数组可以分割成 [1, 5, 5] 和 [11].
```

示例 2:
```
输入: [1, 2, 3, 5]
输出: false
解释: 数组不能分割成两个元素和相等的子集.
```

## 方法
提示：0-1 背包问题，思路是：物品一个一个加进来。

状态：dp[i][j]：考虑索引是 [0,i] 这个区间的物品是否能够填充容量为 j 的背包。

Python 代码：这是用二维数组的写法，试试看能不能用一维数组写出来

```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        _sum = sum(nums)
        if _sum % 2 == 1: return False #总和不是偶数
        
        half = _sum//2
        N = len(nums)
        dp = [[0 for _ in range(half+1)] for _ in range(N)]
        # 先填第 1 行，即 nums[0] 这个物品，是不是能够填满 0，1，。。。，half 的背包
        for j in range(half+1):
            dp[0][j] = True if nums[0]==j else False
        # 再填后面几行
        for i in range(1,N):
            for j in range(half+1):
                # 不放这个物品 、 放这个物品,达到总和sum都可以设为True
                dp[i][j] = dp[i-1][j] or dp[i-1][j-nums[i]]
        return dp[-1][-1]
```

# 322. 零钱兑换

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

示例 1:
```
输入: coins = [1, 2, 5], amount = 11
输出: 3 
解释: 11 = 5 + 5 + 1
```
示例 2:
```
输入: coins = [2], amount = 3
输出: -1
```
说明:
你可以认为每种硬币的数量是无限的。

## 方法：动态规划
dp[i]表示凑够i元所需要的最少硬币数,
那再加一个面值为c的硬币，`dp[i + c] = min(dp[i] + 1, dp[i + c])`

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        dp = [0]+[-1]*amount # 初始化，第一个值为0，其余全是-1
        for i in range(amount):
            if dp[i]<0: #跳过一些无需计算的值
                continue
            for c in coins:
                if i+c > amount:
                    continue # 越界，跳出循环，换下一种硬币
                if dp[i+c]>dp[i]+1 or dp[i+c]<0:
                    dp[i+c] = dp[i]+1
        return dp[amount]
```

# 参考
-   [LeetCode 动态规划专题 7：面试中的 0-1 背包问题](https://www.liwei.party/2018/09/21/leetcode-solution/dynamic-programming-7/)