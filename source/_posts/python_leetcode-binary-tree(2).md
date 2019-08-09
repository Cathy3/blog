---
title: python解leetcode二叉树(2)
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
-   104. 二叉树的最大深度
-   111. 二叉树的最小深度
-   112. 路径总和 (找出树的两个节点之间的最长距离)
-   113. 路径总和 II （找出和为某一值的路径）
-   129. 求根到叶子节点数字之和（找所有路径）
-   257. 二叉树的所有路径  
-   543. 二叉树的直径
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
## 方法:递归
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

# 104.二叉树的最大深度
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

# 112.路径总和
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

# 113. 路径总和 II
给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。

说明: 叶子节点是指没有子节点的节点。

示例:
```
给定如下二叉树，以及目标和 sum = 22，

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
返回:

[
   [5,4,11,2],
   [5,8,4,5]
]
```

## 方法:回溯
注意path传参，要写成path[:]
```python
class Solution:
    def pathSum(self, root: TreeNode, sum: int) -> List[List[int]]:
        res = []
        self.dfs(root,sum,res,[])
        return res
    def dfs(self, root, target, res, path):
        if not root:return
        path += [root.val]
        if sum(path) == target and not root.left and not root.right:
            res.append(path)
            return
        if root.left:
            self.dfs(root.left,target,res,path[:])
        if root.right:
            self.dfs(root.right,target,res,path[:])
        path.pop(-1) # 回溯
```


# 129. 求根到叶子节点数字之和
给定一个二叉树，它的每个结点都存放一个 0-9 的数字，每条从根到叶子节点的路径都代表一个数字。

例如，从根到叶子节点路径 1->2->3 代表数字 123。

计算从根到叶子节点生成的所有数字之和。

说明: 叶子节点是指没有子节点的节点。

示例:
```
输入: [4,9,0,5,1]
    4
   / \
  9   0
 / \
5   1
输出: 1026
解释:
从根到叶子节点路径 4->9->5 代表数字 495.
从根到叶子节点路径 4->9->1 代表数字 491.
从根到叶子节点路径 4->0 代表数字 40.
因此，数字总和 = 495 + 491 + 40 = 1026.
```

## 方法
和257题求所有路径是一样的。

`res = 0`当做参数传给函数，那么函数最后的结果不会影响到res，但是如果把res = [0]即可。
```python
class Solution:
    def sumNumbers(self, root: TreeNode) -> int:
        if not root:return 0
        res = [0]
        self.dfs(root, res, root.val)
        return res[0]
    def dfs(self,root, res, path):
        if not root.left and not root.right:
            res[0] += path
        if root.left:
            self.dfs(root.left, res, path*10+root.left.val)
        if root.right:
            self.dfs(root.right, res, path*10+root.right.val)
```

# 257. 二叉树的所有路径 
给定一个二叉树，返回所有从根节点到叶子节点的路径。

说明: 叶子节点是指没有子节点的节点。

示例:
```
输入:

   1
 /   \
2     3
 \
  5

输出: ["1->2->5", "1->3"]
解释: 所有根节点到叶子节点的路径为: 1->2->5, 1->3
```

## 方法
把path作为字符串，res作为数组保存字符串。

```python
class Solution:
    def binaryTreePaths(self, root: TreeNode) -> List[str]:
        if not root:return []
        res = []
        self.dfs(root, res, ''+str(root.val))
        return res
    def dfs(self, root, res, path):
        if not root.left and not root.right:
            res.append(path)
        if root.left:
            self.dfs(root.left, res, path+'->'+str(root.left.val))
        if root.right:
            self.dfs(root.right, res, path+'->'+str(root.right.val))
```

# 543. 二叉树的直径
给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过根结点。

示例 :
```
给定二叉树

          1
         / \
        2   3
       / \     
      4   5    
返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。
```

注意：两结点之间的路径长度是以它们之间边的数目表示。

## 方法：递归
看叶子节点的左右子树的深度都是0，那么，它的深度是0，一个数的深度是其左右子树的最大值+1。

树总的最大宽度是其左右子树高度的和中的最大值。

求最大距离的过程需要在递归里面写，所以这个步骤比较巧妙，一个递归实现了两个作用。
```python
class Solution:
    def diameterOfBinaryTree(self, root: TreeNode) -> int:
        if not root:
            return 0
        self.diameter = 0
        self.getDepth(root)
        return self.diameter
    
    def getDepth(self, root):
        if not root:
            return 0
        left = self.getDepth(root.left)
        right = self.getDepth(root.right)
        self.diameter = max(self.diameter, left+right)
        return 1 + max(left, right)
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