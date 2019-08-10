---
title: python解leetcode动态规划(2)
date: 2019-08-10
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

**学习内容：**

-   221. 最大正方形
-   115. 不同的子序列


<!-- more -->

# 221. 最大正方形

在一个由 0 和 1 组成的二维矩阵内，找到只包含 1 的最大正方形，并返回其面积。

示例:
```
输入: 

1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0

输出: 4
```

## 方法
设这个DP[i][j]数组为以i, j位置为右下角顶点的能够成的最大正方形的边长。数组如果是第一行或者第一列，显然dp和matrix相等。如果是其他位置，当matrix[i][j] = 1时，能够成的正方形等于左边、上边、左上能够成的正方形边长的最小值+1.为什么是最小值？因为只要存在一个0，那么就没法构成更大的正方形，这个是很保守的策略。

时间复杂度是O(N2)，空间复杂度是O(N2)。
```python
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        if not matrix:return 0
        M = len(matrix)
        N = len(matrix[0])
        dp = [[0]*N for _ in range(M)]
        for i in range(M):
            dp[i][0] = int(matrix[i][0])
        for j in range(N):
            dp[0][j] = int(matrix[0][j])
        for i in range(1,M):
            for j in range(1,N):
                if int(matrix[i][j])==1:
                    dp[i][j] = min(dp[i][j-1], dp[i-1][j], dp[i-1][j-1]) +1
        return max(map(max, dp)) **2
```

# 115.不同的子序列
给定一个字符串 S 和一个字符串 T，计算在 S 的子序列中 T 出现的个数。

一个字符串的一个子序列是指，通过删除一些（也可以不删除）字符且不干扰剩余字符相对位置所组成的新字符串。（例如，"ACE" 是 "ABCDE" 的一个子序列，而 "AEC" 不是）

示例 1:
```
输入: S = "rabbbit", T = "rabbit"
输出: 3
解释:

如下图所示, 有 3 种可以从 S 中得到 "rabbit" 的方案。
(上箭头符号 ^ 表示选取的字母)

rabbbit
^^^^ ^^
rabbbit
^^ ^^^^
rabbbit
^^^ ^^^
```

## 方法
画网格，两个字符串分别为横纵轴，预设空字符串为初始化边界，每一格表示当前位置为止所能匹配的个数。

```python
class Solution:
    def numDistinct(self, s: str, t: str) -> int:
        M, N = len(s), len(t)
        dp = [[0]*(M+1) for _ in range(N+1)] # 加一是考虑空字符串作为边界
        for j in range(M+1):
            dp[0][j] = 1 #T是空字符串的话，在S中匹配一个空字符串
        
        for i in range(1, N+1):
            for j in range(1, M+1): # 可以把i和j分别位于纵轴T和横轴S上。
                if t[i-1] == s[j-1]:
                    # 如果相同，就可以连上左上角的匹配个数，加上左边已有的匹配个数
                    dp[i][j] = dp[i - 1][j - 1] + dp[i][j - 1]
                else: # 如果不同，则保持左边已有的匹配个数
                    dp[i][j] = dp[i][j-1]
        return dp[-1][-1] #右下角为最终匹配个数
```


