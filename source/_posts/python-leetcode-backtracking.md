---
title: 回溯算法
date: 2019-03-16
categories: 
	    -   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
mathjax: true
---

回溯法是暴力搜索法中的一种。 对于某些计算问题而言，回溯法是一种可以找出所有解的一般性算法，尤其适用于约束满足问题。回溯法采用试错的思想，它尝试分步的去解决一个问题。

在分步解决问题的过程中，当它通过尝试发现现有的分步答案不能得到有效的正确的解答的时候，它将取消上一步甚至是上几步的计算，再通过其它的可能的分步解答再次尝试寻找问题的答案。回溯法通常用最简单的递归方法来实现，在反复重复上述的步骤后可能出现两种情况：

-   找到一个可能存在的正确的答案
-   在尝试了所有可能的分步方法后宣告该问题没有答案

回溯算法的基本形式是“**递归+循环**”，正因为循环中嵌套着递归，递归中包含循环，这才使得回溯比一般的递归和单纯的循环更难理解。

**学习目标：**

-   利用回溯算法求解八皇后问题
    -   [leetcode 51. N皇后](https://leetcode-cn.com/problems/n-queens/)
    -   [leetcode 52. N皇后 II](https://leetcode-cn.com/problems/n-queens-ii/)
-   利用回溯算法求解 0-1 背包问题

数组中找所有可能的子集（幂集）

-   78. 子集    （数组中不含重复元素）
-   90. 子集 II （数组中可能含重复元素）

-   [Leetcode 17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)
-   79. 单词搜索

<!-- more -->


# 八皇后问题
有一个棋盘和8个要放到上面的皇后。唯一的要求是皇后之间不能形成威胁。（必须把它们放置成每个皇后都不能吃掉其他皇后的状态）皇后要如何放置呢？

假设不只是8个，而是有任意数目的皇后，怎么解决？

##  方法：回溯 + 生成器
这是一个典型的回溯问题。首先尝试在第一行放置第1个皇后，然后第二行放置第2个，依次类推。 如果发现不能放置下一个皇后，就回溯到上一步，试着将皇后放到其他的位置。最后，或者尝试完所有的可能，或者找到解决方案。


1.  状态表示
    -   元组表示：每个元组中的元素都指示相应行的皇后的列的位置信息。
    -   比如 `state[0] == 3` 就是第一行的皇后在第4列。
2.  寻找冲突
    -   定义一个 conflict 函数，传入已知的皇后位置，判断下一个皇后可能的位置是否与它们有冲突。
    -   冲突指的是水平位置(即列数)相同，或者对角线位置相同(即行数之差==列数之差)。
3.  基本情况：最后一个皇后该怎么做？
    -   已知其他行皇后的位置(相互间没有冲突)，遍历这一行的每一列，返回没有冲突发生的位置。
4.  需要递归的情况
    -   同样已知底层的所有行皇后位置(相互间没有冲突)，判断当前行的这一列没有冲突，则加入包含位置信息的元组中，递归传到下一层，下一层返回它的所有正确位置，加入到当前层的位置信息中。
5.  打包
    -   清理输出。将输出处理得更容易理解一点。

```python
def conflict(state, nextX): #nextX是当前列数
    nextY = len(state) # 当前行数
    for i in range(nextY):# 遍历之前所有行
        if abs(state[i]-nextX) in (0, nextY-i):
            return True # 同一列或同一对角线上，就冲突
    return False

#生成器
def queens(num=8, state=()): 
    for pos in range(num):#遍历所有列
        if not conflict(state,pos): #如果这一列不冲突
            if len(state) == num-1:#到了最后一个皇后
                yield (pos,)
            else:#递归
                for result in queens(num,state+(pos,)):#加上当前层的正确列数pos去下一层
                    #result就是递归的下一层返回来的一个列结果
                    yield (pos,) + result # 当前层应该返回的结果
# 生成器给出所有解决方案
print(list(queens(3)))  # []
print(list(queens(4)))  # [(1, 3, 0, 2), (2, 0, 3, 1)]
# 8个皇后有多少种解决方案：
print(len(list(queens(8))))  # 92

# 打包：更形象得随机输出解决方案
def prettyprint(solution):
    def line(pos,length=len(solution)): # 对于每一行pos
        return '. '*pos + 'X ' + '. '*(length-pos-1)
    for pos in solution:# 对于每一行pos
        print(line(pos))
        
import random
prettyprint(random.choice(list(queens(8))))
#输出：
#. . . . . . X . 
#X . . . . . . . 
#. . X . . . . . 
#. . . . . . . X 
#. . . . . X . . 
#. . . X . . . . 
#. X . . . . . . 
#. . . . X . . . 
```

# 78. 子集
[Leetcode 78. 子集](https://leetcode-cn.com/problems/subsets/)

给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

示例:
```
输入: nums = [1,2,3]
输出:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```
## 方法：回溯，递归，dfs
深度优先算法回溯：以【1,2,3】为例

每轮都传递一个数组起始指针的值，保证遍历顺序：

第一轮：先遍历以1 开头的所有子集，1→12→123 →13

第二轮：遍历以2开头的所有子集，2→23

第三轮:遍历以3开头的所有子集，3

这样三轮遍历保证能找到全部1开头，2开头，3开头的所有子集；同时，每轮遍历后又把上轮的头元素去掉，这样不会出现重复子集。（包括空集）

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        res = []
        nums.sort()
        
        def dfs(nums, index, path, res):
            res.append(path)
            for i in range(index, len(nums)):
                dfs(nums, i+1, path+[nums[i]], res)
        dfs(nums, 0, [],res)
        return res
```
## 方法：组合，非递归

1.  最外层循环：逐一从 nums 数组中取出每个元素 num
2.  内层循环：从原来的结果res集中取出每个中间结果集
    1.  往每个中间结果集中加入 num
    2.  将新的中间结果集加入结果集中

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        res = [[]]
        for num in nums:
            for temp in res[:]:
                x = temp[:]
                x.append(num)
                res.append(x)
        return res
```
相比前一种方法，这个更快

# 90. 子集 II
给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

示例:
```
输入: [1,2,2]
输出:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```
## 方法：递归，dfs
与上面78题的解法应该大体是一样的。我们在进行循环的时候加入一个判断，即新加入的元素是否和刚刚加入的元素相同，如果相同就不加入了。这样就可以屏蔽掉了重复元素的问题。
```python
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        res = []
        nums.sort()
        
        def dfs(nums, index, path, res):
            if path not in res:
                res.append(path)
            for i in range(index, len(nums)):
                if i>index and nums[i]==nums[i-1]:
                    continue
                dfs(nums, i+1, path+[nums[i]], res)
        dfs(nums,0,[],res)
        return res
```


# Leetcode 17. 电话号码的字母组合
给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

示例:
```
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

## 方法：dfs穷举
```python
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        dict = {'2':['a','b','c'],
                '3':['d','e','f'],
                '4':['g','h','i'],
                '5':['j','k','l'],
                '6':['m','n','o'],
                '7':['p','q','r','s'],
                '8':['t','u','v'],
                '9':['w','x','y','z']
                }
        res = []
        length = len(digits)
        
        def dfs(index, path, res):
            if index == length:
                res.append(path)
                return
            for letter in dict[digits[index]]:
                dfs(index+1, path+letter, res)
        
        if length == 0:
            return []    
        dfs(0, '', res)
        return res
```

# 79. 单词搜索
给定一个二维网格和一个单词，找出该单词是否存在于网格中。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

示例:
```
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

给定 word = "ABCCED", 返回 true.
给定 word = "SEE", 返回 true.
给定 word = "ABCB", 返回 false.
```

## 方法：回溯
这个题的回溯的起点可以是二维数组的任意位置。

回溯法的判定条件比较简单，需要注意的是把已经走过的路给改变了，不能再走了。python中通过swapcase()交换该字母的大小写即可行。

```python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        for x in range(len(board)):
            for y in range(len(board[0])):
                if self.existword(board, word, x, y, 0):
                    return True
        return False
    
    def existword(self, board, word, x, y, i):
        if i==len(word): #从第0个开始，word的每个字母都遍历到了
            return True
        if x<0 or x>=len(board) or y<0 or y>=len(board[0]):
            return False
        if board[x][y] != word[i]:
            return False
        
        #该字母存在后，变小写，递归上下左右
        board[x][y] = board[x][y].swapcase()
        isexist = (self.existword(board, word, x+1, y, i+1) or 
                   self.existword(board, word, x, y+1, i+1) or 
                   self.existword(board, word, x-1, y, i+1) or 
                   self.existword(board, word, x, y-1, i+1))
        board[x][y] = board[x][y].swapcase() #变换回来大写
        return isexist
```

# 参考
-   [leetcode 78. Subsets-数组子集|回溯算法](https://blog.csdn.net/happyaaaaaaaaaaa/article/details/51604217)