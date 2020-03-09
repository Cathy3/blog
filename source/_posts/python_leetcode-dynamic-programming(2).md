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
4.  最优子结构：通过求子问题的最优解，可以获得原问题的最优解。

**学习内容：**


-   221. 最大正方形
-   115. 不同的子序列

-   [72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/)  莱文斯坦最短编辑距离

-   找最长公共子串
    -   [718. 最长重复子数组](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)
-   [10. 正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)
-   序列中找到一个和最大的连续子数组
    -   [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)
-   序列中找到一个乘积最大的连续子数组
    -   [152. 乘积最大子序列](https://leetcode-cn.com/problems/maximum-product-subarray/)
-   序列中找到一个递增最长的子数组的长度
    -   [300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

-   121. 买卖股票的最佳时机
-   122. 买卖股票的最佳时机 II
-   309. 最佳买卖股票时机含冷冻期
-   714. 买卖股票的最佳时机含手续费

-   91. 解码方法
-   ❓279. 完全平方数
-   343. 整数拆分

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

# 53. 最大子序和
[Maximum Subarray](https://leetcode-cn.com/problems/maximum-subarray/)

## 题目描述
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例:**

```
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

## 方法：Kadane's algorithm
- 用两个指针：max_so_far 指针记录此前所有碰到的最大和，max_ending_here 指针记录循环到当前元素的最大和。
- 当循环到元素i时，
    - 如果 max_ending_here < 0 的话，说明此前的和是负的，需要舍弃，所以将 max_ending_here 的值变为 i。
    - 反之，表明当前的和还是正值， 将 max_ending_here 的值变为 i + max_ending_here,可以继续向前探索。
    - max_so_far 和 max_ending_here 比较，取较大值。

```python
class Solution(object):
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        max_so_far = max_ending_here = nums[0]
        
        for i in range(1,len(nums)):
            if max_ending_here < 0:
                max_ending_here = nums[i]
            else:
                max_ending_here += nums[i]
            if max_ending_here > max_so_far:
                max_so_far = max_ending_here
        return max_so_far
```

## 方法： 动态规划
首先以第一个元素为一个子序列S1，判断其后的元素j 属不属于S1，应根据元素j 加上 S1的和后，是否比原来的值大。

若sum(S1,j) > sum(S1);则把j添加进S1，往后继续扫描。。

若sum(S1,j) < sum(S1);则j 为另一个（如S2）子序列的开始，以含一个S2的新子序列开始继续扫描。

最后返回和最大的子序列。

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        maxsum = res = nums[0]
        for i in range(1, len(nums)):
            maxsum = max(maxsum + nums[i], nums[i]) # 当前连续的子串
            res = max(maxsum, res)
        return res
```
结果不如前一种方法快

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
思路与 53. 求连续子数组的最大和 相似，都是采用动态规划，`maxvalue[i]`表示以`a[i]`为结尾的子数组中最大乘积，同时维护一个全局最大值`globalmax`。与求子数组的最大和不同的是，还需要维记录子数组最小乘积`minvalue[i]`，因为可能会出现 负 × 负 = 正的情况。并且最大最小乘积只可能出现在 
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


# 1191. K 次串联后最大子数组之和
给你一个整数数组 arr 和一个整数 k。

首先，我们要对该数组进行修改，即把原数组 arr 重复 k 次。

举个例子，如果 arr = [1, 2] 且 k = 3，那么修改后的数组就是 [1, 2, 1, 2, 1, 2]。

然后，请你返回修改后的数组中的最大的子数组之和。

注意，子数组长度可以是 0，在这种情况下它的总和也是 0。

由于 结果可能会很大，所以需要 模（mod） 10^9 + 7 后再返回。 

示例 1：
```
输入：arr = [1,2], k = 3
输出：9
```
示例 2：
```
输入：arr = [1,-2,1], k = 5
输出：2
```
示例 3：
```
输入：arr = [-1,-2], k = 7
输出：0
```

提示：
```
1 <= arr.length <= 10^5
1 <= k <= 10^5
-10^4 <= arr[i] <= 10^4
```

## 题意
求序列S重复K次组成序列的最大子序列和。

## 分析
最原始的做法是拼出展开后的序列，然后使用动态规划求答案。
但是看到 S和K都是 10^5的数量级，序列展开后就是10^10的数量级，内存都爆了。
所以这里不能全部展开。

分析一下可能的答案，发现分两种情况。

1. 如果序列S的总和小于等于0，我们只需要关心两个S即可，后面的都没用。毕竟多一个S总和就会减少一点。

此时答案就是两个序列S拼接后的最优答案（经典的最大连续子序列）。此时答案分两种情况，

    1.  直接是原始S的一个子序列
    2.  是第一个S的后缀加第二个S的前缀。

2.  序列S的总和大于0时，则中间所有的序列S + 两头的序列取前缀和后缀的最大值。 

## 解法
```python
class Solution:
    def kConcatenationMaxSum(self, arr: List[int], k: int) -> int:
        inMax = 0 #单个arr的最大子序列和 
        preMax = 0 #最大子序列后缀
        surMax = 0 #最大子序列前缀
        tot = sum(arr)
        
        for item in arr: # 后缀最大子序列
            preMax = max(item, preMax+item)
            inMax = max(preMax, inMax)
        
        for i in range(len(arr)-1, -1, -1): # 前缀最大子序列
            surMax = max(arr[i], surMax + arr[i])
        
        if k > 1:
            finalMax = max(inMax, surMax + preMax, surMax + preMax + (k- 2) * tot)
        else:
            finalMax = inMax
        
        if finalMax <= 0:
            return 0
        else:
            return(finalMax % (10**9 + 7))
```
[参考链接](https://leetcode.com/problems/k-concatenation-maximum-sum/discuss/382442/ChineseC%2B%2B-1191./344769/)

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

# 121. 买卖股票的最佳时机
[Best Time to Buy and Sell Stock](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

## 题目描述
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

**示例 1:**
```
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
```
**示例 2:**
```
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

## 方法一：遍历
根据题意，我们只需要找出数组中最大的差值即可，即 `max(prices[j] – prices[i]) ，i < j` 。
如何得到最大的差值，只需要一次遍历即可，在遍历的用一个变量记录遍历到当前时的最小值即可。时间复杂度为 `O(n)`.
```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        if prices is None or len(prices)<2:
            return 0
        
        buy = prices[0] # 买入
        profit = 0 # 利润
        
        for i in range(1,len(prices)):
            if prices[i] < buy:
                buy = prices[i]
            else:
                if prices[i] - buy > profit:
                    profit = prices[i] - buy
        return profit
```

## 方法二：Kadane's algorithm
- 方法同上面的53题-求数组中和最大的连续子数组序列。
- 如何转化为求数组中的和最大的连续子序列？相邻两个数作差即可，这样的话子序列的和就是我们在子序列开始卖出股票，在子序列最后买回股票所能得到的收益。
```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        
        max_so_far = max_ending_here = 0
        
        for i in range(1, len(prices)):
            max_ending_here = max(0, max_ending_here + prices[i] - prices[i-1])
            max_so_far = max(max_so_far, max_ending_here)
        return max_so_far
```
测试结果比前一个方法慢

# 122. 买卖股票的最佳时机 II
[Best Time to Buy and Sell Stock II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

## 题目描述
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例 1:**

输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。

**示例 2:**

输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。

**示例 3:**

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

## 方法：遍历
如果后面的股价比前面的大，我们就买卖
```python
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        return sum(max(prices[i+1]-prices[i], 0) for i in range(len(prices)-1))
```

# 309. 最佳买卖股票时机含冷冻期
给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。​

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

-   你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
-   卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

示例:
```
输入: [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```
## 方法
这个题和 714题 比较像。做题方法都是使用了两个数组：

1.  sell 该天结束手里没有股票的情况下，已经获得的最大收益
    -   sell[i]代表的是手里没有股票的收益，这种可能性是今天卖了或者啥也没干。
    -   max(昨天手里有股票的收益+今天卖股票的收益，昨天手里没有股票的收益)， 即 `max(sell[i - 1], hold[i - 1] + prices[i])`；
2.  hold 该天结束手里有股票的情况下，已经获得的最大收益
    -   hold[i]代表的是手里有股票的收益，这种可能性是今天买了股票或者啥也没干，今天买股票必须昨天休息。
    -   max(今天买股票是前天卖掉股票的收益-今天股票的价格，昨天手里有股票的收益）。即 `max(hold[i - 1], sell[i - 2] - prices[i])`。

另外需要注意的是，题目说的是昨天卖了股票的话今天不能买，对于开始的第一天，不可能有卖股票的行为，所以需要做个判断。

该算法的时间复杂度是O(n)，空间复杂度是O(n)。

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices: return 0
        sell = [0] * len(prices) #没有股票的收益
        hold = [0] * len(prices) #持有股票的收益
        hold[0] = -prices[0] #第一天
        for i in range(1, len(prices)):
            sell[i] = max(sell[i-1], hold[i-1] + prices[i])
            hold[i] = max(hold[i-1], (sell[i-2] if i>=2 else 0) - prices[i])
        return sell[-1]
```
如果使用O(1)的空间复杂度，那么就可以写成下面这样：
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices: return 0
        prev_sell = 0
        curr_sell = 0
        hold = -prices[0]
        for i in range(1, len(prices)):
            temp = curr_sell
            curr_sell = max(curr_sell, hold + prices[i])
            hold = max(hold, (prev_sell if i >= 2 else 0) - prices[i])
            prev_sell = temp
        return curr_sell
```

# 714. 买卖股票的最佳时机含手续费
给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每次交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

示例 1:
```
输入: prices = [1, 3, 2, 8, 4, 9], fee = 2
输出: 8
解释: 能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```
注意:

-   0 < prices.length <= 50000.
-   0 < prices[i] < 50000.
-   0 <= fee < 50000.

## 方法:动态规划
该dp使用了两个数字，cash和hold。解释如下：

cash 该天结束手里没有股票的情况下，已经获得的最大收益
hold 该天结束手里有股票的情况下，已经获得的最大收益
所以转移状态分析如下：

cash 更新的策略是：既然今天结束之后手里没有股票，那么可能是今天没买（保持昨天的状态），也可能是今天把股票卖出了

hold 更新的策略是：今天今天结束之后手里有股票，那么可能是今天没卖（保持昨天的状态），也可能是今天买了股票

```python
class Solution:
    def maxProfit(self, prices: List[int], fee: int) -> int:
        cash = 0
        hold = -prices[0]
        for i in range(1,len(prices)):
            #没操作,保持昨天的状态；今天卖出股票
            cash = max(cash, hold+prices[i]-fee) 
            # 没操作,保持昨天的状态；今天买了股票
            hold = max(hold, cash-prices[i])
        return cash
```



# 91. 解码方法
一条包含字母 A-Z 的消息通过以下方式进行了编码：
```
'A' -> 1
'B' -> 2
...
'Z' -> 26
```
给定一个只包含数字的非空字符串，请计算解码方法的总数。

示例 1:
```
输入: "12"
输出: 2
解释: 它可以解码为 "AB"（1 2）或者 "L"（12）。
```
示例 2:
```
输入: "226"
输出: 3
解释: 它可以解码为 "BZ" (2 26), "VF" (22 6), 或者 "BBF" (2 2 6) 。
```
## 方法
和爬楼梯的题目非常像，直接使用dp。

 dp[i]代表解析s[:i]字符串所有可能的方式数目
```python
class Solution:
    def numDecodings(self, s: str) -> int:
        dp = [0]*(len(s)+1)
        dp[0] = 1
        for i in range(1, len(dp)):
            if s[i-1] != '0':
                dp[i] = dp[i-1]
            if i!=1 and '09'<s[i-2:i]<'27':
                dp[i] += dp[i-2]
        return dp[-1]
```

# 279. 完全平方数
给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

示例 1:

输入: n = 12
输出: 3 
解释: 12 = 4 + 4 + 4.
示例 2:

输入: n = 13
输出: 2
解释: 13 = 4 + 9.

## 方法


# 343. 整数拆分
给定一个正整数 n，将其拆分为至少两个正整数的和，并使这些整数的乘积最大化。 返回你可以获得的最大乘积。
 
示例 1:
```
输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1。
```
示例 2:
```
输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36。
说明: 你可以假设 n 不小于 2 且不大于 58。
```

## 分析
暴力解法：回溯遍历将一个数做分割的所有可能性。O(2^n)

## 方法一
观察n从2到13时的情形：
```
2  ->  1 * 1
3  ->  2 * 1
4  ->  2 * 2
5  ->  3 * 2
6  ->  3 * 3
7  ->  3 * 2 * 2
8  ->  3 * 3 * 2
9  ->  3 * 3 * 3
10 ->  3 * 3 * 2 * 2
11 ->  3 * 3 * 3 * 2
12 ->  3 * 3 * 3 * 3
13 ->  3 * 3 * 3 * 2 * 2
```
从上面可以找到如下规律：
```
n / 3 <= 1 时，分为两个数的乘积，尽量均摊
n / 3 > 1 时，分为若干个3和2的乘积
n % 3 == 0 时，分为n个3的乘积
n % 3 == 1 时，分为n-1个3和两个2的乘积
n % 3 == 2 时，分为n个3和一个2的乘积
```
数学证明可以参考LeetCode Discuss：https://leetcode.com/discuss/98276/why-factor-2-or-3-the-math-behind-this-problem

```python
class Solution:
    def integerBreak(self, n: int) -> int:
        div = n // 3
        if div <= 1:
            return (n // 2) * (n // 2 + n % 2)
        mod = n % 3
        if mod == 0:
            return 3 ** div
        elif mod == 1:
            return 3 ** (div - 1) * 4
        elif mod == 2:
            return 3 ** div * 2
```

## 方法二：动态规划

dp[i]表示整数i拆分可以得到的最大乘积，则dp[i]只与dp[i - 2], dp[i - 3]两个状态有关得到状态转移方程：

dp[x] = max(3 * dp[x - 3], 2 * dp[x - 2])

当x <= 3时，需要对结果进行特判。

```python
class Solution:
    def integerBreak(self, n: int) -> int:
        if n <= 3: return n - 1
        dp = [0] * (n + 1)
        dp[2], dp[3] = 2, 3
        for x in range(4, n + 1):
            dp[x] = max(3 * dp[x - 3], 2 * dp[x - 2])
        return dp[n]
```