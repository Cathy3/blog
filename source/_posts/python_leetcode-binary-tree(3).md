---
title: python解leetcode二叉树(3)
date: 2019-08-14
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   二叉树
mathjax: true
---

-   671. 二叉树中第二小的节点
-   230. 二叉搜索树中第K小的元素
<!-- more -->

# 671. 二叉树中第二小的节点
给定一个非空特殊的二叉树，每个节点都是正数，并且每个节点的子节点数量只能为 2 或 0。如果一个节点有两个子节点的话，那么这个节点的值不大于它的子节点的值。 

给出这样的一个二叉树，你需要输出所有节点中的第二小的值。如果第二小的值不存在的话，输出 -1 。

示例 1:
```
输入: 
    2
   / \
  2   5
     / \
    5   7

输出: 5
说明: 最小的值是 2 ，第二小的值是 5 。
```
示例 2:
```
输入: 
    2
   / \
  2   2

输出: -1
说明: 最小的值是 2, 但是不存在第二小的值。
```
## 方法一
遍历所有值，找最小，再找次小

使用了一个中序遍历，把所有的值放入到set里，然后我们先找最小值，然后删除掉它之后，再求一次最小值就是次小值。
```python
class Solution:
    def findSecondMinimumValue(self, root: TreeNode) -> int:
        self.res = set()
        self.inOrder(root)
        if len(self.res) <= 1: return -1
        min1 = min(self.res)
        self.res.remove(min1)
        return min(self.res)
    def inOrder(self, root):
        if not root:return
        self.inOrder(root.left)
        self.res.add(root.val)
        self.inOrder(root.right)
```

## 方法二
所有的值的最小值一定是root的值。

可以使用一个变量，时刻保存现在遇到的比最小值大并且比次小值小的值作为第二小的值。

```python
class Solution:
    def findSecondMinimumValue(self, root: TreeNode) -> int:
        if not root: return -1
        self.res = float("inf")
        self.min = root.val
        self.inOrder(root)
        return self.res if self.res != float("inf") else -1

    def inOrder(self, root):
        if not root:
            return
        self.inOrder(root.left)
        if self.min < root.val < self.res:
            self.res = root.val
        self.inOrder(root.right)
```

# 230. 二叉搜索树中第K小的元素

## 方法：递归
提到BST就应该想到它的中序遍历是有序的，因此，最简单的想法就是获取中序遍历的list，直接得到第k个数即可。方法很简单，注意中序遍历的写法，以及，获取第k个数的List标号是k-1.

当K比较小的时候，没必要获取得到所有的中序遍历结果之后再停止，可以直接记数到了k停止即可，这样后面的结果就不用统计了。

我们需要做的是有一个全局的变量保存现在已经遍历了多少节点了，或者还剩多少个节点需要遍历。
```python
class Solution:
    def kthSmallest(self, root: TreeNode, k: int) -> int:
        self.res = 0
        self.count = 0
        self.inOrder(root, k)
        return self.res
    def inOrder(self, root, k):
        if not root:return
        self.inOrder(root.left, k)
        self.count += 1
        if self.count == k: 
            self.res = root.val
            return
        self.inOrder(root.right, k)
```

# 参考
-   [【LeetCode】230. Kth Smallest Element in a BST 解题报告（C++ & Java）
](https://blog.csdn.net/fuxuemingzhu/article/details/69947495)