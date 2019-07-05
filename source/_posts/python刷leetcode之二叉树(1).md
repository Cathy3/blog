---
title: python刷leetcode之二叉树(1)
date: 2019-06-21
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   二叉树
mathjax: true
---

-   100. 相同的树  (两棵树是否相同)
-   101. 对称二叉树 （一个二叉树是否对称）
-   111. 二叉树的最小深度
-   113.  
-   129. 
-   257.   
-   298. 
-   687. 最长同值路径
-   783. 二叉搜索树结点最小距离
-   938. 二叉搜索树的范围和

<!-- more -->

# 100. 相同的树
给定两个二叉树，编写一个函数来检验它们是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

示例 1:
```
输入:       1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

输出: true
```
示例 2:
```
输入:      1          1
          /           \
         2             2

        [1,2],     [1,null,2]

输出: false
```
示例 3:
```
输入:       1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

输出: false
```
## 方法
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isSameTree(self, p: TreeNode, q: TreeNode) -> bool:
        if not p and not q:
            return True
        if not p or not q: 
            return False
        if p.val == q.val:
            return self.isSameTree(p.left, q.left) and self.isSameTree(p.right, q.right)
        else:
            return False
```



# 101. 对称二叉树
给定一个二叉树，检查它是否是镜像对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:
```
    1
   / \
  2   2
   \   \
   3    3
```

## 方法一：DFS
一个二叉树遍历的过程中不一定一定只有一个参数，也可以同时传了两个参数分别是左右好处是可以直接比较，当然这个时候需要把root放到两个参数上进行比较。

递归很重要的一部分是终止条件的确定。

```python
class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        if not root:
            return True
        return self.isMirror(root.left, root.right)
    
    def isMirror(self, left, right):
        if not left and not right:
            return True
        if not left or not right:
            return False
        if left.val != right.val:
            return False
        
        return self.isMirror(left.left, right.right) and self.isMirror(left.right, right.left)
```

# 111. 二叉树的最小深度
给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

说明: 叶子节点是指没有子节点的节点。

示例:
```
给定二叉树 [3,9,20,null,null,15,7],

    3
   / \
  9  20
    /  \
   15   7
返回它的最小深度  2.
```

## 方法：递归 + BFS
发现这层有个叶子的话，就直接返回就行了
叶子的深度是1

```python
class Solution:
    def minDepth(self, root: TreeNode) -> int:
        if not root:
            return 0 # 当root为空的时候直接返回0
        left = self.minDepth(root.left)
        right = self.minDepth(root.right)
        if not left:# 判断树的深度应该到叶子节点，也就是左右子结点都为空的那个结点
            return right + 1 
        if not right:
            return left + 1
        return 1 + min(left, right)
```


# 687. 最长同值路径
给定一个二叉树，找到最长的路径，这个路径中的每个节点具有相同值。 这条路径可以经过也可以不经过根节点。

注意：两个节点之间的路径长度由它们之间的边数表示。

示例 1:
```
输入:

              5
             / \
            4   5
           / \   \
          1   1   5
输出:2
```
示例 2:
```
输入:

              1
             / \
            4   5
           / \   \
          4   4   5
输出:2
```
注意: 给定的二叉树不超过10000个结点。 树的高度不超过1000。

## 方法:递归 + dfs
求一个顶点到所有根节点的路径，时刻保留相等元素的最大值。相等元素的最大值是左右子树的相等元素的最大值+1，所以是递归。

定义的DFS函数是获得在通过root节点的情况下，最长单臂路径。其中更新的res是左右臂都算上的。所以这个题和普通的题是有点不一样。

```python
class Solution:
    def longestUnivaluePath(self, root: TreeNode) -> int:
        longest = [0]
        def dfs(root):
            if not root:
                return 0
            left_len, right_len = dfs(root.left), dfs(root.right)
            left = left_len + 1 if root.left and root.left.val == root.val else 0 
            right = right_len + 1 if root.right and root.right.val == root.val else 0
            longest[0] = max(longest[0], left + right)
            return max(left, right)
        dfs(root)
        return longest[0]
```

# 783. 二叉搜索树结点最小距离
该题和[530. 二叉搜索树的最小绝对差](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/) 一样。可以用相同方法解题。

给定一个二叉搜索树的根结点 root, 返回树中任意两节点的差的最小值。

示例：
```
输入: root = [4,2,6,1,3,null,null]
输出: 1
解释: 注意，root是树结点对象(TreeNode object)，而不是数组。
```
给定的树 [4,2,6,1,3,null,null] 可表示为下图:
```
          4
        /   \
      2      6
     / \    
    1   3  
```
最小的差值是 1, 它是节点1和节点2的差值, 也是节点3和节点2的差值。

注意：

二叉树的大小范围在 2 到 100。

二叉树总是有效的，每个节点的值都是整数，且不重复。


## 方法：中序遍历+递归
中序遍历，得到有序列表，然后找出相邻的两个节点差值的最小值

```python
class Solution:
    def minDiffInBST(self, root: TreeNode) -> int:
        vals = []
        def inOrder(root):
            if not root:
                return 
            inOrder(root.left)
            vals.append(root.val)
            inOrder(root.right)
        inOrder(root)
        return min([vals[i+1]-vals[i] for i in range(len(vals)-1)])
```


# 938. 二叉搜索树的范围和
给定二叉搜索树的根结点 root，返回 L 和 R（含）之间的所有结点的值的和。

二叉搜索树保证具有唯一的值。

示例 1：
```
输入：root = [10,5,15,3,7,null,18], L = 7, R = 15
输出：32
```
示例 2：
```
输入：root = [10,5,15,3,7,13,18,1,null,6], L = 6, R = 10
输出：23
```
提示：树中的结点数量最多为 10000 个。最终的答案保证小于 2^31。

## 方法：BST+递归
```python
class Solution:
    def rangeSumBST(self, root: TreeNode, L: int, R: int) -> int:
        if not root:
            return 0
        res = 0
        if L <= root.val <= R:
            res += root.val
            res += self.rangeSumBST(root.left, L, R)
            res += self.rangeSumBST(root.right, L, R)
        elif root.val < L:
            res += self.rangeSumBST(root.right, L, R)
        elif root.val > R:
            res += self.rangeSumBST(root.left, L, R)
        return res
```

# 参考
-   [687. Longest Univalue Path 解题报告（Python & C++）- 负雪明烛](https://blog.csdn.net/fuxuemingzhu/article/details/79248926)