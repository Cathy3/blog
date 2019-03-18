---
title: python实现数据结构之二叉树
date: 2019-03-09
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   二叉树
mathjax: true
---
二叉查找树（英语：Binary Search Tree），也称为二叉搜索树、有序二叉树（ordered binary tree）或排序二叉树（sorted binary tree），是指一棵空树或者具有下列性质的二叉树：

1.   若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
2.   若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；
3.   任意节点的左、右子树也分别为二叉查找树；
4.   没有键值相等的节点。

二叉查找树相比于其他数据结构的优势在于查找、插入的时间复杂度较低，为 $O(log n)$。 每次插入的新的结点都是二叉查找树上新的叶子结点，在进行插入操作时，不必移动其它结点，只需改动某个结点的指针，由空变为非空即可。

学习目标：

-   实现一个二叉查找树，并且支持插入、删除、查找操作
-   实现查找二叉查找树中某个节点的后继、前驱节点
-   实现二叉树前、中、后序以及按层遍历
-   leetcode上的验证二叉搜索树(98)及
二叉树   层次遍历(102,107)！（选做）（保留往期第四天任务）

226. Invert Binary Tree（翻转二叉树）

104. Maximum Depth of Binary Tree（二叉树的最大深度）

112. Path Sum（路径总和）

# LeetCode 701. 二叉搜索树中的插入操作
[Insert into a Binary Search Tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 保证原始二叉搜索树中不存在新值。

注意，可能存在多种有效的插入方式，只要树在插入后仍保持为二叉搜索树即可。 你可以返回任意有效的结果。

例如, 
```
给定二叉搜索树:

        4
       / \
      2   7
     / \
    1   3

和 插入的值: 5
```
你可以返回这个二叉搜索树:
```
         4
       /   \
      2     7
     / \   /
    1   3 5
```
或者这个树也是有效的:
```
         5
       /   \
      2     7
     / \   
    1   3
         \
          4
```
# 方法
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def insertIntoBST(self, root: TreeNode, val: int) -> TreeNode:
        if root:
            if val < root.val:
                root.left = self.insertIntoBST(root.left, val)
            else:
                root.right = self.insertIntoBST(root.right, val)
            return root
        else:
            return TreeNode(val)
```

# LeetCode 700. 二叉搜索树中的搜索
[Search in a Binary Search Tree](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

给定二叉搜索树（BST）的根节点和一个值。 你需要在BST中找到节点值等于给定值的节点。 返回以该节点为根的子树。 如果节点不存在，则返回 NULL。

例如，
```
给定二叉搜索树:

        4
       / \
      2   7
     / \
    1   3

和值: 2
```
你应该返回如下子树:
```
      2     
     / \   
    1   3
```
# 方法
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def searchBST(self, root: TreeNode, val: int) -> TreeNode:
        if not root:
            return None
        if val == root.val:
            return root        
        if val < root.val:
            return self.searchBST(root.left, val)
        else:
            return self.searchBST(root.right, val)
```
# LeetCode 450. 删除二叉搜索树中的节点
[Delete Node in a BST](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的 key 对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

一般来说，删除节点可分为两个步骤：

首先找到需要删除的节点；
如果找到了，删除它。
说明： 要求算法时间复杂度为 O(h)，h 为树的高度。

示例:
```
root = [5,3,6,2,4,null,7]
key = 3

    5
   / \
  3   6
 / \   \
2   4   7

给定需要删除的节点值是 3，所以我们首先找到 3 这个节点，然后删除它。

一个正确的答案是 [5,4,6,2,null,null,7], 如下图所示。

    5
   / \
  4   6
 /     \
2       7

另一个正确答案是 [5,2,6,null,4,null,7]。

    5
   / \
  2   6
   \   \
    4   7
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
    def deleteNode(self, root: TreeNode, key: int) -> TreeNode:
        if not root:
            return None
        if root.val == key: # 找到这个结点
            if not root.right: # 如果只有左孩子，或没有孩子
                return root.left
            else: # 如果有右孩子
                succ = root.right
                while succ.left:# 找右子树的最小值，即待删结点的后继
                    succ = succ.left
                succ.val, root.val = root.val, succ.val #交换两个值
        root.left = self.deleteNode(root.left, key) # 递归遍历左子树
        root.right = self.deleteNode(root.right, key) # 递归遍历右子树
        return root
```
#
前驱节点

若一个节点有左子树，那么该节点的前驱节点是其左子树中val值最大的节点（也就是左子树中所谓的rightMostNode）

若一个节点没有左子树，那么判断该节点和其父节点的关系

2.1 若该节点是其父节点的右边孩子，那么该节点的前驱结点即为其父节点。

2.2 若该节点是其父节点的左边孩子，那么需要沿着其父亲节点一直向树的顶端寻找，直到找到一个节点P，P节点是其父节点Q的右边孩子，那么Q就是该节点的前驱节点


后继节点

若一个节点有右子树，那么该节点的后继节点是其右子树中val值最小的节点（也就是右子树中所谓的leftMostNode）

若一个节点没有右子树，那么判断该节点和其父节点的关系

2.1 若该节点是其父节点的左边孩子，那么该节点的后继结点即为其父节点

2.2 若该节点是其父节点的右边孩子，那么需要沿着其父亲节点一直向树的顶端寻找，直到找到一个节点P，P节点是其父节点Q的左边孩子，那么Q就是该节点的后继节点

## 方法
写成递归形式，

-    当根节点值小于等于p节点值，说明p的后继节点一定在右子树中，
     -   所以对右子节点递归调用此函数，

-    如果根节点值大于p节点值，那么有可能根节点就是p的后继节点，或者左子树中的某个节点是p的后继节点，
     -    所以先对左子节点递归调用此函数，如果返回空，说明根节点是后继节点，返回即可，如果不为空，则将那个节点返回
##
## 中序遍历

中序遍历二叉查找树可得到一个关键字的有序序列，一个无序序列可以通过构造一棵二叉查找树变成一个有序序列，构造树的过程即为对无序序列进行查找的过程。

# 参考
-    《Problem Solving with Algorithms and Data Structures using Python》
-    [[LeetCode] Inorder Successor in BST 二叉搜索树中的中序后继节点](http://www.cnblogs.com/grandyang/p/5306162.html)