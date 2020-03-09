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
动态规划方法可以画网格⭐。

1.  递归 + 记忆化 ——> 递推（动态规划）（自上而下的记忆化搜索）
2.  状态的定义：数组 opt[n], dp[n], fib[n] 
3.  状态转移方程：opt[n] = best_of(opt[n-1], opt[n-2], ...) 从前面的n-1个值得到最优的第n个值
4.  最优子结构

动态规划是自下而上的解决问题。（先解决小数据量下的问题怎么解决，之后层层递推更大的数据量问题。）

将原问题拆解成若干子问题，同时保存子问题的答案，使得每个子问题只求解一次，最终获得原问题的答案。

**学习内容：**

-   [509. 斐波那契数列](https://leetcode-cn.com/problems/fibonacci-number/)
-   [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

-   62. 不同路径
-   63. 不同路径 II
-   [64. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)
-   [120. 三角形最小路径和](https://leetcode-cn.com/problems/triangle/)

-   198. 打家劫舍
-   213. 打家劫舍 II
-   337. 打家劫舍 III



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

-   题目中说每个格子里是非负整数，负数会有什么影响？
-   限定的只能左移或下移，如果没有限定，上下左右都可以，会怎么办？

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

# 198. 打家劫舍
你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

示例 1:
```
输入: [1,2,3,1]
输出: 4
解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```
示例 2:
```
输入: [2,7,9,3,1]
输出: 12
解释: 偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

## 方法一
假设只有一家，那么你只能偷这家；假设有两家，那么你要判断两家哪个钱多，偷哪个；依次类推，假设有n家，那么你要判断“偷第n家不偷第n-1家且前n-2家尽量多的偷”和“不偷第n家且前n-1家尽量多的偷”，哪个得到的钱多偷哪个。 
你可以递归求解，然而复杂度太高无法AC。所以应该记录已经计算过的结果，于是这变成一个动态规划问题。

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) == 0: return 0
        elif len(nums)<2: return max(nums[0], nums[-1])
        
        money = [0]*len(nums)
        money[0],money[1] = nums[0], max(nums[0], nums[1])
        for i in range(2, len(nums)):
            money[i] = max(nums[i] + money[i-2], money[i-1])
        return money[len(nums)-1]
```

## 方法二：更简洁的写法
上 面的代码使用的空间是冗余的，因为每次循环只会用到前两个数据。所以代码可以降低空间复杂度到O(1)。
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        now = last = 0
        for i in nums:
            last, now = now, max(i+last, now)
        return now
```

# 213. 打家劫舍 II
你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

示例 1:
```
输入: [2,3,2]
输出: 3
解释: 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
```
示例 2:
```
输入: [1,2,3,1]
输出: 4
解释: 你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
     偷窃到的最高金额 = 1 + 3 = 4 。
```
## 方法
这个是198题的拓展。本题目里面的房间是一个环的，也就是说第一个房子和最后一个房子是相邻的。在这种情况下，相邻的两个房子不能一起偷，求能偷到的金额的最大值。

多了环的条件，在这个约束下就多了个不同时偷第一个和最后一个就可以了。所以，两种偷的情况：第一种不偷最后一个房间，第二种不偷第一个房间，求这两种偷法能获得的最大值。所以只多了一个切片的过程。

状态转移方程仍然是：
```
dp[0] = num[0] （当i=0时） 
dp[1] = max(num[0], num[1]) （当i=1时） 
dp[i] = max(num[i] + dp[i - 2], dp[i - 1]) （当i !=0 and i != 1时）
```
第三个式子就是当前的房间偷的情况和不偷情况下的最大值。

时间复杂度是O(N)，空间复杂度是O(N).

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums: return 0
        N = len(nums)
        if N == 1: return nums[0]
        if N == 2: return max(nums[0], nums[1])
        return max(self.rob_range(nums[0: N-1]), self.rob_range(nums[1:N]))
    
    def rob_range(self, nums):
        if not nums: return 0
        N = len(nums)
        if N == 1: return nums[0]
        if N == 2: return max(nums[0], nums[1])
        
        dp = [0]*N
        dp[0] = nums[0]
        dp[1] = max(nums[0], nums[1])
        for i in range(2, N):
            dp[i] = max(dp[i-1], dp[i-2] + nums[i])
        return dp[-1]
```



# 337. 打家劫舍 III
在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

示例 1:
```
输入: [3,2,3,null,3,null,1]

     3
    / \
   2   3
    \   \ 
     3   1

输出: 7 
解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.
```
示例 2:
```
输入: [3,4,5,1,3,null,1]

     3
    / \
   4   5
  / \   \ 
 1   3   1

输出: 9
解释: 小偷一晚能够盗取的最高金额 = 4 + 5 = 9.
```

## 方法
就是求本节点+孙子更深节点vs儿子节点+重孙更深的节点的比较。

用了dfs函数，虽然递归是自顶向下的，但是因为是不断的return，所以真正求值是从底向上的。

用到了一个有两个元素的列表，分别保存了之前层的，不取节点和取节点的情况。然后遍历左右子树，求出当前节点取和不取能得到的值，再返回给上一层。

注意这个里面的robcur是当前节点能达到的最大值，所以最后返回结果的时候试试返回的root节点robcur的值

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def rob(self, root: TreeNode) -> int:
        
        def dfs(root):
            if not root: return [0, 0]
            left = dfs(root.left)
            right = dfs(root.right)
            norob = left[1] + right[1] #本节点不抢劫，儿子节点的最大值
            robcur = max(root.val + left[0] + right[0], norob)
            return [norob, robcur]
        return dfs(root)[1]   
```






# 参考
-   [【LeetCode】120. Triangle 解题报告（Python）](https://blog.csdn.net/fuxuemingzhu/article/details/82883187)