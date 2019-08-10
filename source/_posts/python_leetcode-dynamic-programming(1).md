---
title: python解leetcode动态规划(1)
date: 2019-04-05
categories: 
	    -   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   动态规划
mathjax: true
---
动态规划主要方法是画网格⭐。

1.  递归 + 记忆化 ——> 递推（动态规划）
2.  状态的定义：数组 opt[n], dp[n], fib[n] 
3.  状态转移方程：opt[n] = best_of(opt[n-1], opt[n-2], ...) 从前面的n-1个值得到最优的第n个值
4.  最优子结构

**学习内容：**

-   [LeetCode 509. 斐波那契数列](https://leetcode-cn.com/problems/fibonacci-number/)
-   [LeetCode 70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)
-   0-1 背包问题
    -   自我实现
    -   Palindrome Partitioning II(132)
-   62. 不同路径
-   63. 不同路径 II
-   [LeetCode 64. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)
-   编程实现莱文斯坦最短编辑距离
    -   [LeetCode 72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/)
-   找最长公共子串
    -   [LeetCode718. 最长重复子数组](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)
-   [LeetCode 10. 正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)
-   [LeetCode 120. 三角形最小路径和](https://leetcode-cn.com/problems/triangle/)
-   [LeetCode 152. 乘积最大子序列](https://leetcode-cn.com/problems/maximum-product-subarray/)
-   [LeetCode 300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)
-   [LeetCode 322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)


<!-- more -->

# 509. 斐波那契数列求值 
$f(n)=f(n-1)+f(n-2)$ 

斐波那契数列由 0 和 1 开始，后面的每一项数字都是前面两项数字的和。

[0,1,1,2,3,5,8,13,...]  给定`n`,计算 `f(N)`

## 方法：动态规划

-   递归 + 记忆化 -> 递推
-   递推公式：A[i] = A[i-1] + A[i-2]

```python
class Solution:
    def fib(self, N: int) -> int:
        if (N <= 1):
            return N
        A = [None]*(N+1)
        A[0] = 0
        A[1] = 1
        for i in range(2, N+1):
            A[i] = A[i-1] + A[i-2]
        return A[N]
```
更简洁的写法：
```python
class Solution:
    def fib(self, N: int) -> int:
        if (N <= 1):
            return N
        previous = 0
        current = 1
        for _ in range(N-1):
            previous, current = current, previous + current         
        return current
```

# 70. 爬楼梯
Climbing Stairs

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

## 方法：动态规划
当有 $n$ 个台阶时，可供选择的走法可以分两类：1，先跨一阶再跨完剩下 $n-1$ 阶；2，先跨2阶再跨完剩下 $n-2$ 阶。所以 $n$ 阶的不同走法的数目是  $n-1$ 阶和 $n-2$ 阶的走法数的和。 这和斐波那契数列的规律相同，可以用这个思路。

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


# 62. 不同路径
一个机器人位于一个 m x n 网格的左上角。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

问总共有多少条不同的路径？
示例 1:
```
输入: m = 3, n = 2
输出: 3
解释:
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向右 -> 向下
2. 向右 -> 向下 -> 向右
3. 向下 -> 向右 -> 向右
```

## 方法
每一格子都是左边和上边之和
```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        grid = [[0]*n for _ in range(m)]
        for i in range(n):
            grid[0][i] = 1
        for i in range(m):
            grid[i][0] = 1
        for i in range(1,m):
            for j in range(1,n):
                grid[i][j] = grid[i][j-1] + grid[i-1][j]
        return grid[m-1][n-1]
```

# 63. 不同路径 II
一个机器人位于一个 m x n 网格的左上角。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

示例 1:
```
输入:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
输出: 2
解释:
3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```

## 方法
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        m, n = len(obstacleGrid), len(obstacleGrid[0])
        dp = [[0]*n for _ in range(m)]
        if obstacleGrid[0][0] == 0:
            dp[0][0] = 1
        
        for i in range(m):
            for j in range(n):
                if obstacleGrid[i][j] == 1:
                    dp[i][j] = 0
                else:
                    if i!=0:#不是最上面一行
                        dp[i][j] += dp[i-1][j]
                    if j!=0:
                        dp[i][j] += dp[i][j-1]
        return dp[m-1][n-1]
```

# 64. 最小路径和
给定一个包含非负整数的 m x n 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步。

示例:
```
输入:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 7
解释: 因为路径 1→3→1→1→1 的总和最小。
```

## 方法：动态规划
新的一个点的最短路径一定等于其上方、左方最短路径+当前的值。

-   第一行的每个状态 = 左边状态+当前位置
-   第一列的每个状态 = 上边状态+当前位置。
-   其余位置 = 上边和左边的状态最小值 + 当前位置。

```python
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        if not grid or not grid[0]: return 0
        m,n = len(grid),len(grid[0]) # 行列数
        
        for i in range (m):
            for j in range(n):
                if i==0 and j==0:
                    before = 0
                elif i==0:
                    before = grid[0][j-1]
                elif j==0:
                    before = grid[i-1][0]
                else:
                    before = min(grid[i-1][j], grid[i][j-1])
                grid[i][j] = before + grid[i][j]
        return grid[m-1][n-1]
```

# 72. 编辑距离
给定两个单词 word1 和 word2，计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：
```
插入一个字符
删除一个字符
替换一个字符
```

示例 1:
```
输入: word1 = "horse", word2 = "ros"
输出: 3
解释: 
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```
示例 2:
```
输入: word1 = "intention", word2 = "execution"
输出: 5
解释: 
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

## 方法：递归
判断最后的一个字符是否相等。

-   如果相等，那么只用判断前面的子串即可，
-   如果不等，需要前面子串变成相等之后+1(删、增、换) 把word1变成word2。

```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        L1, L2 = len(word1), len(word2)
        dp = [[-1]*(L2+1) for _ in range(L1+1)] # 初始化全部小于0
        return self.getDistance(word1, word2, dp, L1, L2)
    def getDistance(self, word1, word2, dp, pos1, pos2):
        if pos1 == 0: return pos2 # pos表示当前位置，也表示前面子串的长度
        if pos2 == 0: return pos1
        if dp[pos1][pos2] >= 0: return dp[pos1][pos2] # 当前位置的操作数
        
        res = 0
        if word1[pos1-1] == word2[pos2-1]:
            res = self.getDistance(word1, word2, dp, pos1-1, pos2-1)
        else:
            res = 1 + min(self.getDistance(word1, word2, dp, pos1-1, pos2),#如果删除后，剩下的前面子串的操作数
                          self.getDistance(word1, word2, dp, pos1, pos2-1),#如果插入后，剩下的前面子串的操作数
                          self.getDistance(word1, word2, dp, pos1-1, pos2-1))#如果替换后，剩下的前面子串的操作数
        dp[pos1][pos2] = res
        return res
```

## 方法：动态规划
上一个方法很容易改成动态规划。事实上，这个题的动态规划比记忆化搜索还要慢。
```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        L1, L2 = len(word1), len(word2)
        dp = [[0] * (L2 + 1) for _ in range(L1 + 1)]
        for i in range(L1 + 1):
            dp[i][0] = i
        for j in range(L2 + 1):
            dp[0][j] = j
        for i in range(1, L1 + 1):
            for j in range(1, L2 + 1):
                if word1[i - 1] == word2[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1]
                else:
                    dp[i][j] = 1 + min(dp[i - 1][j],
                                       dp[i][j - 1],
                                       dp[i - 1][j - 1])
        return dp[L1][L2]
```



# 718. 最长重复子数组
给两个整数数组 A 和 B ，返回两个数组中公共的、长度最长的子数组的长度。

示例 1:
```
输入:
A: [1,2,3,2,1]
B: [3,2,1,4,7]
输出: 3
解释: 长度最长的公共子数组是 [3, 2, 1]。
```
说明:

1.  1 <= len(A), len(B) <= 1000
2.  0 <= A[i], B[i] < 100

## 方法:动态规划
是找到当前节点之前连续的公共子串长度问题。

画格子：横纵坐标分别是A和B，对应比较，相等时，格子为1，或者左上角的数+1。


状态转移就2种，要么上一个匹配成功，要么这一个匹配成功，其他情况都为0。

我们可以用匹配矩阵来做，让行作为第一个字符串，列为第二个字符串，那么 dp[row][col] 则是第一个字符串第 row 位置和第二个字符串第 col 位置最大的匹配长度。

递推关系为，dp[i][j] = dp[i-1][j-1]+1，当A[i]== B[j]。如果不等的话，dp[i][j]为0.

```python 
class Solution:
    def findLength(self, A: List[int], B: List[int]) -> int:
        m, n = len(A), len(B)
        dp = [[0 for j in range(n)] for i in range(m)]
        for i in range(m):
            for j in range(n):
                if A[i] == B[j]:
                    if i==0 or j==0:
                        dp[i][j]=1
                    else:
                        dp[i][j] = dp[i-1][j-1] + 1
        return max(map(max,dp))
```





# 10. 正则表达式匹配
给定一个字符串 (s) 和一个字符模式 (p)。实现支持 '.' 和 '*' 的正则表达式匹配。
```
'.' 匹配任意单个字符。
'*' 匹配零个或多个前面的元素。
```
匹配应该覆盖整个字符串 (s) ，而不是部分字符串。

说明:

-   s 可能为空，且只包含从 a-z 的小写字母。
-   p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。

示例 1:
```
输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
```
示例 2:
```
输入:
s = "aa"
p = "a*"
输出: true
解释: '*' 代表可匹配零个或多个前面的元素, 即可以匹配 'a' 。因此, 重复 'a' 一次, 字符串可变为 "aa"。
```
示例 3:
```
输入:
s = "ab"
p = ".*"
输出: true
解释: ".*" 表示可匹配零个或多个('*')任意字符('.')。
```
示例 4:
```
输入:
s = "aab"
p = "c*a*b"
输出: true
解释: 'c' 可以不被重复, 'a' 可以被重复一次。因此可以匹配字符串 "aab"。
```
示例 5:
```
输入:
s = "mississippi"
p = "mis*is*p*."
输出: false
```

## 方法：动态规划
动态方程 `dp[i][j]` 表示 $s$ 中 [0, i-1] 这前 $i$ 个字符与 $p$ 的 [0, j-1] 这前 $j$ 个字符组成的表示式是否匹配。

$$
dp[i+1][j+1] =
\begin{cases}
dp[i][j],  & \text{p[j] == '.'  or  (s[i] == p[j ])} 
\\[2ex]
dp[i+1][j-1]  , & \text{p[j] == '*' and p[j-1] 匹配0次}
\\[2ex]
dp[i+1][j], &\text{p[j] == '*' and p[j -1] 匹配1次}
\\[2ex]
dp[i][j+1],&\text{p[j] == '*' and p[j-1] 匹配超过1次}
\end{cases}
$$


```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        dp = [[False for _ in range(len(p)+1)] for _ in range(len(s)+1)]
        dp[0][0] = True
        for j in range(len(p)):
            if p[j]=='*': # 星号匹配空
                dp[0][j+1] = dp[0][j-1]
                
        for i in range(len(s)):
            for j in range(len(p)):
                if p[j] == s[i] or p[j]=='.':# 按位比较，相等
                    dp[i+1][j+1] = dp[i][j]
                if p[j] =='*':# 星号分两种情况
                    if p[j-1] != s[i] and p[j-1]!='.': #匹配0次
                        dp[i+1][j+1] = dp[i+1][j-1]
                    else: # 匹配多次、1次、0次
                        dp[i+1][j+1] = (dp[i][j+1] or dp[i+1][j] or dp[i+1][j-1])
        return dp[len(s)][len(p)]
```

# 120. 三角形最小路径和
给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

例如，给定三角形：
```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
自顶向下的最小路径和为 11（即，2 + 3 + 5 + 1 = 11）。
```

## 方法：从下到上的动态规划
从顶向下的DP会导致元素越来越多，因此不是很方便，从下向上的DP做。

用`minpath[k][i]`保存从下向上得到的第 k 层第 i 个位置的最短路径。那么有：

`minpath[k][i] = min( minpath[k+1][i], minpath[k+1][i+1]) + triangle[k][i]`

可以看出 `minpath[k][i]` 只被用到了一次，所以可以变成一维DP：
```
For the kth level:
minpath[i] = min( minpath[i], minpath[i+1]) + triangle[k][i];
```
注意，
-   dp的初始化应该是最下面一层，然后从倒数第二层开始遍历；
-   第layer的元素是layer + 1个。

时间复杂度为O(n^2)，空间复杂度O(n)。n为三角形高度。


```python
class Solution:
    def minimumTotal(self, triangle: List[List[int]]) -> int:
        dp = triangle[-1]
        for layer in range(len(triangle)-2, -1, -1):
            for i in range(layer+1):
                dp[i] = min(dp[i], dp[i+1]) + triangle[layer][i]
        return dp[0]
    
```

# 152. 乘积最大子序列
给定一个整数数组 nums ，找出一个序列中乘积最大的连续子序列（该序列至少包含一个数）。

示例 1:
```
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```
示例 2:
```
输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```

## 方法：动态规划
思路与 [53. 求连续子数组的最大和](https://cathy3.github.io/2019/02/22/%E7%94%A8python%E5%88%B7LeetCode%E6%95%B0%E7%BB%84%E9%A2%98(%E7%AE%80%E5%8D%95%E7%BA%A7)(%E4%B8%80)/) 相似，都是采用动态规划，`maxvalue[i]`表示以`a[i]`为结尾的子数组中最大乘积，同时维护一个全局最大值`globalmax`。与求子数组的最大和不同的是，还需要维记录子数组最小乘积`minvalue[i]`，因为可能会出现 负 × 负 = 正的情况。并且最大最小乘积只可能出现在 
`maxvalue[i−1]×a[i], minvalue[i−1]×a[i], a[i]`三者之间。

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        maxvalue = minvalue = nums[0]
        globalmax = nums[0]
        for i in range(1, len(nums)):
            lastmax = maxvalue
            maxvalue = max(minvalue * nums[i], lastmax * nums[i], nums[i])#当前最大
            minvalue = min(minvalue * nums[i], lastmax * nums[i], nums[i])#当前最小
            globalmax = max(globalmax, maxvalue) # 全局最大
        return globalmax
```

# 300. 最长上升子序列
给定一个无序的整数数组，找到其中最长上升子序列的长度。

示例:
```
输入: [10,9,2,5,3,7,101,18]
输出: 4 
解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
```
说明:

-   可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。
-   你算法的时间复杂度应该为 O(n2) 。

进阶: 你能将算法的时间复杂度降低到 O(n log n) 吗?

## 方法：动态规划
数组 dp 的 `dp[i]` 是保存到第 i 位置为止的最长递增子序列的长度。

最后求所有位置的最大值，而不是 dp 的最后元素。

递推式：

```
循环位置i之前的所有元素 j ：
    如果 num[j] < nums[i]:
        当前最大dp[i] = max{dp[i], dp[j]+1},此时dp[j]已经是j位置为止的最大长度
```

```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums: return 0
        dp = [1]*len(nums)
        
        for i in range(1, len(nums)):
            for j in range(0,i):
                if nums[j] < nums[i]:
                    dp[i] = max(dp[i], dp[j]+1)
        return max(dp)
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
-   [【LeetCode】120. Triangle 解题报告（Python）](https://blog.csdn.net/fuxuemingzhu/article/details/82883187)