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
-   235. 二叉搜索树的最近公共祖先
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
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

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

# 235. 二叉搜索树的最近公共祖先
[Lowest Common Ancestor of a Binary Search Tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

示例 1:
```
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
输出: 6 
解释: 节点 2 和节点 8 的最近公共祖先是 6。
```
示例 2:
```
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
输出: 2
解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。
``` 

说明:

-   所有节点的值都是唯一的。
-   p、q 为不同节点且均存在于给定的二叉搜索树中。

## 方法:递归
BST本身的属性，所以比较节点的值和根节点的值的大小就知道下一步去哪里查找了。
```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if min(p.val, q.val) <= root.val and max(p.val, q.val) >= root.val:
            return root
        elif p.val < root.val and q.val < root.val:
            return self.lowestCommonAncestor(root.left, p, q)
        elif p.val > root.val and q.val > root.val:
            return self.lowestCommonAncestor(root.right, p, q)
```
这个题是236. 二叉树的最近公共祖先 的特例，所以可以直接使用236的代码就能通过。

# 236. 二叉树的最近公共祖先
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

示例 1:
```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
```
示例 2:
```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
```

说明:

-   所有节点的值都是唯一的。
-   p、q 为不同节点且均存在于给定的二叉树中。

## 方法
这个题的模式叫做devide and conquer. 如果当前节点等于其中的p和q某一个节点，那么找到了节点，返回该节点，否则在左右子树分别寻找。

左右子树两个返回的是什么呢？按照该递归函数的定义，即找到了左子树和右子树里p和q的公共祖先，注意祖先可以是节点自己。然后根据左右侧找到的节点做进一步的判断。

如果左右侧查找的结果都不为空，说明分别找到了p和q，那么LCA就是当前节点。否则就在不为空的那个结果就是所求。

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if not root or p==root or q==root:
            return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        return left if left else right
```



# 参考
-   [【LeetCode】230. Kth Smallest Element in a BST 解题报告（C++ & Java）
](https://blog.csdn.net/fuxuemingzhu/article/details/69947495)