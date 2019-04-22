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
二叉查找树（英语：Binary Search Tree），也称为二叉搜索树、有序二叉树（ordered binary tree）或排序二叉树（sorted binary tree）。

**学习目标：**

-   实现一个二叉查找树，并且支持插入、删除、查找操作
    -   LeetCode 108. 将有序数组转换为二叉搜索树
    -   LeetCode 109. 有序链表转换二叉搜索树
    -   LeetCode 701. 二叉搜索树中的插入操作
    -   LeetCode 700. 二叉搜索树中的搜索
    -   LeetCode 450. 删除二叉搜索树中的结点
-   实现查找二叉查找树中某个结点的后继、前驱结点
-   实现二叉树前、中、后序以及按层遍历
    -   LeetCode 144. 二叉树的前序遍历
    -   LeetCode 94. 二叉树的中序遍历
    -   LeetCode 145. 二叉树的后序遍历
    -   LeetCode 102. 二叉树的层次遍历
-   LeetCode 98. 验证二叉搜索树
-   LeetCode 103. 二叉树的锯齿形层次遍历
-   LeetCode 226. 翻转二叉树
-   LeetCode 104. 二叉树的最大深度
-   LeetCode 112. 路径总和
 
<!-- more -->

二叉查找树是指：一棵空树，或者具有下列性质的二叉树：

1.   若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
2.   若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；
3.   任意节点的左、右子树也分别为二叉查找树；
4.   没有键值相等的节点。

二叉查找树相比于其他数据结构的优势在于查找、插入的时间复杂度较低，为 $O(log n)$。 每次插入的新的结点都是二叉查找树上新的叶子结点，在进行插入操作时，不必移动其它结点，只需改动某个结点的指针，由空变为非空即可。

# 将有序数组转换为二叉搜索树
[LeetCode 108. Convert Sorted Array to Binary Search Tree](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

示例:
```
给定有序数组: [-10,-3,0,5,9],

一个可能的答案是：[0,-3,9,-10,null,5]，它可以表示下面这个高度平衡二叉搜索树：

      0
     / \
   -3   9
   /   /
 -10  5
```

## 方法：递归
由于要求二叉查找树是平衡的。所以可以选在数组的中间那个数当树根root。

然后这个数左边的数组为左子树，右边的数组为右子树，分别递归产生左右子树就可以了。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> TreeNode:        
        length = len(nums)
        if length == 0:
            return None
        if length == 1:
            return TreeNode(nums[0])
        middle = (length-1)//2
        root = TreeNode(nums[middle])
        root.left = self.sortedArrayToBST(nums[:middle])
        root.right = self.sortedArrayToBST(nums[middle+1:])
        return root
```

# 有序链表转换二叉搜索树

[LeetCode 109. Convert Sorted List to Binary Search Tree](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

给定一个单链表，其中的元素按升序排序，将其转换为高度平衡的二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

示例:
```
给定的有序链表： [-10, -3, 0, 5, 9],

一个可能的答案是：[0, -3, 9, -10, null, 5], 它可以表示下面这个高度平衡二叉搜索树：

      0
     / \
   -3   9
   /   /
 -10  5
```

## 方法：链表转成数组
转成数组后，方法同上一题108的解法

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def sortedListToBST(self, head: ListNode) -> TreeNode:
        nums = []
        while head:
            nums.append(head.val)
            head = head.next
            
        def sortedArrayToBST(nums):
            length = len(nums)
            if length == 0:
                return None
            if length == 1:
                return TreeNode(nums[0])
            middle = (length-1)//2
            root = TreeNode(nums[middle])
            root.left = sortedArrayToBST(nums[:middle])
            root.right = sortedArrayToBST(nums[middle+1:])
            return root
        
        return sortedArrayToBST(nums)
```

# 二叉搜索树中的插入操作
[LeetCode 701. Insert into a Binary Search Tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

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

## 方法
与根结点比较大小，递归左子树或右子树。

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

# 二叉搜索树中的搜索
[LeetCode 700. Search in a Binary Search Tree](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

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

## 方法
与根结点比较大小，等于则返回，大于小于则 **递归** 左子树或右子树。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
#    def sortedArrayToBST(self, nums: [int]) -> TreeNode:
#        #层序树的列表转为树结构
#        l=len(nums)
#        nodeLst = []
#        for i in range(l):
#            nodeLst.append(TreeNode(nums[i]))
#        for i in range(l//2-1):
#            nodeLst[i].left = nodeLst[2*i+1]
#            nodeLst[i].right = nodeLst[2*i+2]
#        nodeLst[l//2-1].left = nodeLst[2*(l//2-1)+1]
#        if len(nums)%2 == 1:
#             nodeLst[l//2-2].left = nodeLst[2*(l//2-1)+1]
#        return nodeLst[0]
#
#    def BSTTraversal(self,root): #层序输出树的列表
#        a = []
#        L = []
#        L.append(root)
#        while L:
#            if L[0].left is not None:
#                L.append(L[0].left)  
#            if L[0].right is not None:
#                L.append(L[0].right)
#            a.append(L.pop(0).val)
#        return a 
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
# 删除二叉搜索树中的节点
[LeetCode 450. Delete Node in a BST](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

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
1.  递归遍历整个树
2.  找到这个要删除的结点后，如果只有左孩子，或没有孩子，返回左孩子或空
3.  如果有右孩子，找右子树的最小值，即待删结点的后继，交换两个值。

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

# 查找后继节点
查找二叉查找树中某个节点的前驱节点

## 思路
1. 若一个节点有右子树，那么该节点的后继节点是其右子树中val值最小的节点（也就是右子树中所谓的leftMostNode）

2. 若一个节点没有右子树，那么判断该节点和其父节点的关系

    1.  若该节点是其父节点的左边孩子，那么该节点的后继结点即为其父节点

    2.  若该节点是其父节点的右边孩子，那么需要沿着其父亲节点一直向树的顶端寻找，直到找到一个节点P，P节点是其父节点Q的左边孩子，那么Q就是该节点的后继节点

## 方法
写成递归形式，

-    当根节点值小于等于p节点值，说明p的后继节点一定在右子树中，
     -   所以对右子节点递归调用此函数

-    如果根节点值大于p节点值，那么有可能根节点就是p的后继节点，或者左子树中的某个节点是p的后继节点，
     -    所以先对左子节点递归调用此函数，如果返回空，说明根节点是后继节点，返回即可，如果不为空，则将那个节点返回
```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def sortedArrayToBST(self, nums: [int]) -> TreeNode:
        #层序树的列表转为树结构
        l=len(nums)
        nodeLst = []
        for i in range(l):
            nodeLst.append(TreeNode(nums[i]))
        for i in range(l//2-1):
            nodeLst[i].left = nodeLst[2*i+1]
            nodeLst[i].right = nodeLst[2*i+2]
        nodeLst[l//2-1].left = nodeLst[2*(l//2-1)+1]
        if len(nums)%2 == 1:
             nodeLst[l//2-2].left = nodeLst[2*(l//2-1)+1]
        return nodeLst[0]    

    def searchSuccNode(self, root: TreeNode, key: int) -> TreeNode:
        if not root:
            return None
        if root.val <= key: # 后继在右子树中
             return self.searchSuccNode(root.right, key)
        else: # 后继就是该结点，或者在该结点的左子树中。
            left = self.searchSuccNode(root.left, key)
            if not left:
                return root 
            else: return left
    
if __name__ == '__main__':
    root = [4,2,7,1,3,5]
    p = 5
    S = Solution()
    root = S.sortedArrayToBST(root)
    
    r= S.searchSuccNode(root,p) 
    if not r:
        print(str(p) + '的后继不存在')
    else:
        print(str(p) + '的后继是' + str(r.val)) 
#输出：5的后继是7
```
# 查找前驱结点
查找二叉查找树中某个节点的前驱节点

## 思路
1.  若一个节点有左子树，那么该节点的前驱节点是其左子树中val值最大的节点（也就是左子树中所谓的rightMostNode）

2.  若一个节点没有左子树，那么判断该节点和其父节点的关系

    1.  若该节点是其父节点的右边孩子，那么该节点的前驱结点即为其父节点。

    2.  若该节点是其父节点的左边孩子，那么需要沿着其父亲节点一直向树的顶端寻找，直到找到一个节点P，P节点是其父节点Q的右边孩子，那么Q就是该节点的前驱节点

## 方法
方法和查找后继结点相同。只是左右子树比较相反。

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def sortedArrayToBST(self, nums: [int]) -> TreeNode:
        #层序树的列表转为树结构
        l=len(nums)
        nodeLst = []
        for i in range(l):
            nodeLst.append(TreeNode(nums[i]))
        for i in range(l//2-1):
            nodeLst[i].left = nodeLst[2*i+1]
            nodeLst[i].right = nodeLst[2*i+2]
        nodeLst[l//2-1].left = nodeLst[2*(l//2-1)+1]
        if len(nums)%2 == 1:
             nodeLst[l//2-2].left = nodeLst[2*(l//2-1)+1]
        return nodeLst[0]
    
    def searchPredecessorNode(self, root: TreeNode, key: int) -> TreeNode:
        if not root:
            return None
        if root.val >= key: # 前驱在左子树中
             return self.searchPredecessorNode(root.left, key)
        else: # 后继就是该结点，或者在该结点的左子树中。
            right = self.searchPredecessorNode(root.right, key)
            if not right:
                return root 
            else: return right
    
if __name__ == '__main__':
    root = [4,2,7,1,3,5]
    p = 3
    S = Solution()
    root = S.sortedArrayToBST(root)
    
    r= S.searchPredecessorNode(root,p) 
    if not r:
        print(str(p) + '的前驱不存在')
    else:
        print(str(p) + '的前驱是' + str(r.val))
```

# 二叉树的前序遍历
[LeetCode 144. Binary Tree Preorder Traversal](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

给定一个二叉树，返回它的 前序 遍历。

 示例:
```
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [1,2,3]
```

## 方法一：用栈从上到下遍历
先右孩子入栈，再左孩子入栈。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        stack = [root] # 用栈来辅助存储
        res = []
        while stack:
            node = stack.pop() 
            res.append(node.val)
            if node.right: stack.append(node.right)
            if node.left: stack.append(node.left)
        return res
```

## 方法二：递归

```python
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        res = []
        def dfs(root, res):
            if root:
                res.append(root.val)
                dfs(root.left, res)
                dfs(root.right, res)
            return res
        return dfs(root,res)
```

## 方法三：用栈迭代
从根节点開始，一直找它的左子树，直到为空，再找右子树。
```python
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        stack = [] # 用栈来辅助存储
        res = []
        cur = root
        while stack or cur:
            while cur: #从根节点開始，一直找它的左子树，直到cur为空
                stack.append(cur) # 栈中存入根节点和左孩子
                res.append(cur.val)
                cur = cur.left
            cur = stack.pop().right #找右子树
        return res
```

# 二叉树的中序遍历
[LeetCode 94. Binary Tree Inorder Traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

给定一个二叉树，返回它的中序 遍历。

示例:
```
输入: [1,null,2,3]
   1
    \
     2
    /
   3

输出: [1,3,2]
```

## 方法一：递归

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        res = []
        def inorder(root, res):
            if root:                
                inorder(root.left, res)
                res.append(root.val)
                inorder(root.right, res)
            return res
        return inorder(root,res)
```

## 方法二：用栈迭代
```python
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        stack = [] # 用栈来辅助存储
        res = []
        cur = root
        while stack or cur:
            while cur: #从根节点開始，一直找它的左子树，直到cur为空
                stack.append(cur) # 栈中存入根节点和左孩子                
                cur = cur.left
            temp = stack.pop() # 出栈，则左孩子先出来，然后才是根结点
            res.append(temp.val)
            cur = temp.right #找右子树
        return res
```

# 二叉树的后序遍历
[LeetCode 145. Binary Tree Postorder Traversal](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

给定一个二叉树，返回它的 后序 遍历。

示例:
```
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [3,2,1]
```

## 方法一：递归
```python
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        res = []
        def postorder(root, res):
            if root:                
                postorder(root.left, res)
                postorder(root.right, res)
                res.append(root.val)
            return res
        return postorder(root,res)  
```

## 方法二：迭代
```python
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        if not root:
            return []
        stack = [] # 用栈来辅助存储
        res = []
        cur = root
        while stack or cur:
            while cur: #从根节点開始，一直找它的左子树，直到cur为空
                stack.append(cur) # 栈中存入根节点和右孩子
                res.append(cur.val)
                cur = cur.right
            cur = stack.pop().left #找左子树
        return res[::-1] 
```

# 二叉树的层次遍历
[LeetCode 102. Binary Tree Level Order Traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

给定一个二叉树，返回其按层次遍历的节点值。 （即逐层地，从左到右访问所有节点）。

例如:
给定二叉树: [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```
返回其层次遍历结果：
```
[
  [3],
  [9,20],
  [15,7]
]
```

## 方法一： 广度优先搜索（BFS）
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if not root:
            return []
        res = []
        currLevel = [root]
        while currLevel:
            res.append([node.val for node in currLevel])
            nextLevel = []
            for node in currLevel:
                if node.left:
                    nextLevel.append(node.left)
                if node.right:
                    nextLevel.append(node.right)
            currLevel = nextLevel
        return res
```

## 方法二：深度优先搜索（DFS）
```python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        def dfs(root, depth, res):
            if not root:
                return res
            if len(res) <= depth:
                res.append([])
            res[depth].append(root.val)
            dfs(root.left, depth+1,res)
            dfs(root.right,depth+1,res)
        res = []
        dfs(root,0,res)
        return res
```

# 二叉树的锯齿形层次遍历
[LeetCode 103. Binary Tree Zigzag Level Order Traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

给定一个二叉树，返回其节点值的锯齿形层次遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

例如：
给定二叉树 [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```
返回锯齿形层次遍历如下：
```
[
  [3],
  [20,9],
  [15,7]
]
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
    def zigzagLevelOrder(self, root: TreeNode) -> List[List[int]]:
        def dfs(root, depth, res):
            if not root:
                return res
            if len(res) <= depth:
                res.append([])
            if depth % 2 == 0: 
                res[depth].append(root.val)
            else:
                res[depth].insert(0, root.val)
            dfs(root.left, depth+1,res)
            dfs(root.right,depth+1,res)
        res = []
        dfs(root,0,res)
        return res
```

# 验证二叉搜索树
[LeetCode 98. Validate Binary Search Tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

节点的左子树只包含小于当前节点的数。
节点的右子树只包含大于当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。
示例 1:
```
输入:
    2
   / \
  1   3
输出: true
```
示例 2:
```
输入:
    5
   / \
  1   4
     / \
    3   6
输出: false
解释: 输入为: [5,1,4,null,null,3,6]。
     根节点的值为 5 ，但是其右子节点值为 4 。
```
## 方法一：中序遍历
中序遍历按照 左孩子－根节点－右孩子 这个顺序遍历，如果每个子树都满足左孩子<根节点<右孩子，那就应该是个BST吧。
```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        res = []
        def inorder(root, res):
            if root:                
                inorder(root.left, res)
                res.append(root.val)
                inorder(root.right, res)
            return res
        inorder(root,res)
        return res == sorted(res) and len(res) == len(set(res))
```

# 方法二：递归
左子树的值要在(min,mid)之间，右子树的值在(mid,max)之间，这个mid值并不是中位数而是当前节点的值。

定义一个辅助函数，
-   要给这个辅助函数传入：当前要判断的节点、当前要判断的这个节点的取值下限和取值上限。
-   然后使用递归即可，每次要计算下一个节点的时候都要根据这个节点是左孩子还是右孩子对其取值的区间进行更新。

```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        return self.valid(root, float('-inf'), float('inf'))

    def valid(self, root, min, max):
        if not root:
            return True
        if root.val >= max or root.val <= min:
            return False
        return self.valid(root.left, min, root.val) and self.valid(root.right, root.val, max)
```

# 翻转二叉树
[LeetCode 226. Invert Binary Tree](https://leetcode-cn.com/problems/invert-binary-tree/)

翻转一棵二叉树。

示例：
```
输入：

     4
   /   \
  2     7
 / \   / \
1   3 6   9

输出：

     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

## 方法一：递归
DFS 每次递归交换当前节点的左右子树，同时对左右子树做同样的处理。
```python
class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return None
        temp = root.left
        root.left = self.invertTree(root.right)
        root.right = self.invertTree(temp)
        return root
```
## 方法二：栈
```python
class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return None
        stack = [root]
        while len(stack) != 0:
            node = stack.pop()
            node.left, node.right = node.right, node.left
            if node.left:
                stack.append(node.left)
            if node.right:
                stack.append(node.right)
        return root
```

# 二叉树的最大深度
[LeetCode 104. Maximum Depth of Binary Tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

示例：
给定二叉树 [3,9,20,null,null,15,7]，
```
    3
   / \
  9  20
    /  \
   15   7
```
返回它的最大深度 3 。

## 方法一：深度优先搜索
深度优先搜索（DFS），递归求解。
```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

## 方法二：广度优先搜索
广度优先搜索（BFS），利用队列求解。
```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        
        depth = 0
        q = [root]
        while len(q) != 0:
            depth += 1
            for _ in range(len(q)):
                if q[0].left:
                    q.append(q[0].left)
                if q[0].right:
                    q.append(q[0].right)
                del q[0]
        return depth
```

# 路径总和
[LeetCode 112. Path Sum](https://leetcode-cn.com/problems/path-sum/)

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

说明: 叶子节点是指没有子节点的节点。

示例: 
给定如下二叉树，以及目标和 sum = 22，
```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1
```
返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。

## 方法一：DFS + 递归
用深度优先搜索（DFS）遍历所有可能的从根到叶的路径，要注意每深一层要从和中减去相应节点的数值。下面是递归实现的代码。
```python
class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        if not root:
            return False
        if root.left or root.right:
            return self.hasPathSum(root.left, sum-root.val) or self.hasPathSum(root.right, sum-root.val)
        else:
            return True if sum == root.val else False
```

## 方法二：DFS + 栈
DFS的非递归实现，用栈实现。
```python
class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        stack = [(root, sum)]
        while len(stack) != 0:
            node, tmp_sum = stack.pop()
            if node:
                if not node.left and not node.right and node.val == tmp_sum:
                    return True
                stack.append((node.right, tmp_sum-node.val))
                stack.append((node.left, tmp_sum-node.val))
        return False
```
## 方法三：BFS + 队列
```python
class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        queue = [(root, sum)]
        while len(queue) != 0:
            node, tmp_sum = queue.pop()
            if node:
                if not node.left and not node.right and node.val == tmp_sum:
                    return True
                queue.insert(0, (node.right, tmp_sum-node.val))
                queue.insert(0, (node.left, tmp_sum-node.val))
        return False
```

## 方法四：后序遍历 + 栈
直接将路径保存在栈中，每次进入不同的层不需要记录当前的和
```python
class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        pre, cur = None, root
        tmp_sum = 0
        stack = []
        while cur or len(stack) > 0:
            while cur:
                stack.append(cur)
                tmp_sum += cur.val
                cur = cur.left   # 最左子树
            cur = stack[-1]
            if not cur.left and not cur.right and tmp_sum == sum:
                return True
            if cur.right and pre != cur.right:
                cur = cur.right # 右子树
            else:
                pre = cur
                stack.pop()  # 左子树
                tmp_sum -= cur.val
                cur = None
        return False
```

# 参考
-    《Problem Solving with Algorithms and Data Structures using Python》
-    [[LeetCode] Inorder Successor in BST 二叉搜索树中的中序后继节点](http://www.cnblogs.com/grandyang/p/5306162.html)
-   [226. Invert Binary Tree [easy] (Python)](https://blog.csdn.net/coder_orz/article/details/51383933)
-   [112. Path Sum [easy] (Python)](https://blog.csdn.net/coder_orz/article/details/51595815)