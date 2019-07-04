---
title: 用python刷LeetCode链表题(中等级)
date: 2019-04-05
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 链表
---
-   2. 两数相加
-   19. 删除链表的倒数第N个节点
-   24. 两两交换链表中的节点
-   61. 旋转链表
-   82. 删除排序链表中的重复元素 II
-   86. 分隔链表
-   92. 反转链表 II
-   109. 有序链表转换二叉搜索树
    -   (解法见 [python实现数据结构之二叉树](https://cathy3.github.io/2019/03/09/python%E5%AE%9E%E7%8E%B0%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%8B%E4%BA%8C%E5%8F%89%E6%A0%91/))
-   138. [复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)
-   142. [环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
-   143. 重排链表
-   147. 对链表进行插入排序
-   148. 排序链表
-   328. 

<!-- more -->

# 2. 两数相加
给出两个**非空**的链表用来表示两个非负的整数。其中，它们各自的位数是按照**逆序**的方式存储的，并且它们的每个节点只能存储**一位**数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：
```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

## 方法：偷懒做法
先求和，再构建链表。这个方法比较暴力。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        num1 = ''
        num2 = ''
        while l1:#243
            num1 += str(l1.val)
            l1 = l1.next
        while l2:#564
            num2 += str(l2.val)
            l2 = l2.next
        add = str(int(num1[::-1]) + int(num2[::-1]))[::-1] #342+465=807---708
        head = ListNode(add[0])
        res = head
        for i in range(1,len(add)):
            node = ListNode(add[i])
            head.next = node
            head = head.next
        return res
```

# 19. 删除链表的倒数第N个节点
给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

示例：
```
给定一个链表: 1->2->3->4->5, 和 n = 2.
当删除了倒数第二个节点后，链表变为 1->2->3->5.
```
说明：

给定的 n 保证是有效的。

## 方法：快慢指针
用两个指针，一个快指针从前到后扫描整个链表，另一个慢指针的步数差n+1，那么当p指向尾部的Null时，指针q恰好指向要删除节点的前一个节点。由于可能删除头部节点，伪装一个新的头部方便操作。

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        new_head = ListNode(0)
        new_head.next = head
        fast = slow = new_head
        for i in range(n+1):
            fast = fast.next
        while fast:
            fast = fast.next
            slow = slow.next
        slow.next = slow.next.next # 删除第n个
        return new_head.next
```


# 24. 两两交换链表中的节点
[Swap Nodes in Pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

## 题目描述
给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例:
```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```

## 方法
a, b, pre 三个变量

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        pre, pre.next = self, head
        while pre.next and pre.next.next: #考虑奇数和偶数
            a = pre.next 
            b = a.next # a,b是要调换的相邻元素
            pre.next, b.next, a.next =b, a, b.next # next连线重置
            pre = a
        return self.next
```

# 61. 旋转链表
给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。

示例 1:
```
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL
```

示例 2:
```
输入: 0->1->2->NULL, k = 4
输出: 2->0->1->NULL
解释:
向右旋转 1 步: 2->0->1->NULL
向右旋转 2 步: 1->2->0->NULL
向右旋转 3 步: 0->1->2->NULL
向右旋转 4 步: 2->0->1->NULL
```

## 方法
首先，可以从Example 2也能看出来，存在k>len的情况，这样必须求余运算，否则肯定超时。即要求链表的长度。

其次，如果求余之后，知道了移动几次，本质上就是把链表的后面k个节点移动到开头去。注意是平移，顺序不变的。所以要找到后面的k个节点，那么需要用到19. Remove Nth Node From End of List类似的方法，用两个距离为k的指针进行平移操作，当前面的到达了末尾，那么后面的正好是倒数第k个。

找到倒数第k个之后，那么把这个节点和之前的节点断开，把后面的这段移到前面去即可。

```python
class Solution:
    def rotateRight(self, head: ListNode, k: int) -> ListNode:
        if not head or not head.next: return head
        root = head
        _len = 0
        while head:
            _len += 1
            head = head.next
        k %= _len
        if k == 0: return root
        
        fast, slow = root,root
        while k-1:
            fast = fast.next
            k -= 1
        pre = slow
        while fast.next:
            fast = fast.next
            pre = slow
            slow = slow.next
        pre.next = None
        fast.next = root
        return slow
```

# 82. 删除排序链表中的重复元素 II
给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

示例 1:
```
输入: 1->2->3->3->4->4->5
输出: 1->2->5
```
示例 2:
```
输入: 1->1->1->2->3
输出: 2->3
```
## 方法
注意审题啊，这个distinct的意思并不是去重，而是删除出现次数不止一次的。

去重的可以看这个题：83. Remove Duplicates from Sorted List

因此必须先遍历一遍，统计每个节点出现的次数。

第二次遍历的时候，查找下个节点的值出现的次数如果不是1次，那么就删除下个节点。修改这个节点的下个指针指向下下个节点，这是指向该节点位置的指针不要动，因为还要判断新的next值。

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        root = ListNode(0)
        root.next = head
        valist = []
        while head:
            valist.append(head.val)
            head = head.next
        counter = collections.Counter(valist)
        
        head = root
        while head and head.next:
            if counter[head.next.val] != 1:
                head.next = head.next.next
            else:
                head = head.next
        return root.next
```

# 86. 分隔链表
给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于 x 的节点都在大于或等于 x 的节点之前。

你应当保留两个分区中每个节点的初始相对位置。

示例:
```
输入: head = 1->4->3->2->5->2, x = 3
输出: 1->2->2->4->3->5
```
## 方法
做链表的题，不要省指针。

用两个新指针，分别记录比x值小的和比x值大的，遍历原来的链表的时候根据值的大小拼接到对应的链表后面。

最后再把两个链表拼接到一起就行了。
```python
class Solution:
    def partition(self, head: ListNode, x: int) -> ListNode:
        small = ListNode(0)
        large = ListNode(0)
        small_root, large_root = small, large
        while head:
            if head.val < x:
                small.next = head
                small = small.next
            else:
                large.next = head
                large = large.next
            head = head.next
        large.next = None
        small.next = large_root.next
        return small_root.next
```

# 92. 反转链表 II
反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。

说明:
1 ≤ m ≤ n ≤ 链表长度。

示例:
```
输入: 1->2->3->4->5->NULL, m = 2, n = 4
输出: 1->4->3->2->5->NULL
```
## 方法
其实就是翻转链表的而变形题目了。做法大家都能想出来，进行一遍遍历，把第m到n个元素进行翻转，即依次插入到第m个节点的头部。这个题还是有意思的。建议后面再多做几遍。

```python
class Solution:
    def reverseBetween(self, head: ListNode, m: int, n: int) -> ListNode:
        count = 1
        root = ListNode(1)
        root.next = head
        pre = root
        while pre.next and count<m:
            pre = pre.next
            count += 1
        if count < m:
            return head
        
        mNode = pre.next
        curr = mNode.next
        while curr and count<n:
            Next = curr.next
            curr.next = pre.next
            pre.next = curr
            mNode.next = Next
            curr = Next
            count += 1
        
        return root.next
```

# 138. 复制带随机指针的链表
给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。

要求返回这个链表的深拷贝。 

示例：
```
输入：
{"$id":"1","next":{"$id":"2","next":null,"random":{"$ref":"2"},"val":2},"random":{"$ref":"2"},"val":1}

解释：
节点 1 的值是 1，它的下一个指针和随机指针都指向节点 2 。
节点 2 的值是 2，它的下一个指针指向 null，随机指针指向它自己。
 
```
提示：

你必须返回给定头的拷贝作为对克隆列表的引用。

## 方法
使用hashtable，在这个hash表里，记录了老链表和新链表的每一组对应。这样先构造了一个纯next的链表，然后再次循环就能得到带random的链表了。

```python
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        nodeDict = dict()
        dummy = Node(0, None, None)
        nodeDict[head] = dummy
        newHead, pointer = dummy, head
        
        while pointer:
            nodeDict[pointer] = Node(pointer.val, pointer.next, None)
            newHead.next = nodeDict[pointer]
            newHead, pointer = newHead.next, pointer.next
        
        pointer = head
        while pointer:
            if pointer.random:
                nodeDict[pointer].random = nodeDict[pointer.random]
            pointer = pointer.next
        return dummy.next
```

# 142. 环形链表 II
给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

说明：不允许修改给定的链表。

示例 1：
```
输入：head = [3,2,0,-4], pos = 1
输出：tail connects to node index 1
解释：链表中有一个环，其尾部连接到第二个节点。
```

## 方法一：set()
如果我们保存走过的每个位置不就好了吗？所以，对于Python来说，可以直接把对象放到set中去，这样，当我们再次遍历到已经访问过的节点时，说明有了环，直接返回该节点即可。
```python
class Solution(object):
    def detectCycle(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head: return None
        
        visited = set()
        while head:
            if head in visited:
                return head
            visited.add(head)
            head = head.next
        return None
```

## 方法二：快慢指针

```python
class Solution(object):
    def detectCycle(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        slow, fast = head, head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            if fast == slow: # 相遇点
                break
        if not fast or not fast.next:
            return None
        
        slow = head # 从头开始走
        while slow != fast:
            slow = slow.next # 从头开始走
            fast = fast.next # 从相遇点开始走
        return fast 
```

# 143. 重排链表
给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例 1:
```
给定链表 1->2->3->4, 重新排列为 1->4->2->3.
```
示例 2:
```
给定链表 1->2->3->4->5, 重新排列为 1->5->2->4->3.
```
## 方法
题目大意:把一个链表的前半部分正序，后半部分逆序，然后一个一个的连接起来。
```python
class Solution(object):
    def reorderList(self, head):
        """
        :type head: ListNode
        :rtype: None Do not return anything, modify head in-place instead.
        """
        
        if head and head.next and head.next.next:
            
            # 找中间位置head2
            fast, slow = head, head
            while fast.next and fast.next.next:
                fast = fast.next.next
                slow = slow.next
            head1 = head
            head2 = slow.next
            slow.next = None # 前半部分结尾空
            
            # 反转head2后半部分
            pre = ListNode(0)
            pre.next = head2
            curr = head2.next
            head2.next = None # 后半部分结尾空
            while curr:
                temp = curr
                curr = curr.next
                temp.next = pre.next
                pre.next = temp
            head2 = pre.next

            # 两部分交叉连接
            p1, p2 = head1, head2
            while p2:
                temp1 = p1.next
                temp2 = p2.next
                p1.next = p2
                p2.next = temp1
                p1 = temp1
                p2 = temp2
```

# 147. 对链表进行插入排序
插入排序算法：

1.  插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
2.  每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
3.  重复直到所有输入数据插入完为止。

## 方法
```python
class Solution(object):
    def insertionSortList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head or not head.next: return head
        pre = ListNode(0)
        pre.next = head
        while head.next: # 遍历每个元素，如果小于就插入前面
            if head.val <= head.next.val:
                head = head.next
            else:
                temp = head.next # temp用来跟前面的有序数列进行依次比较
                head.next = head.next.next # 删掉该元素
                p = pre #有序数列从前开始比较
                while p.next and p.next.val<= temp.val:
                    p = p.next
                temp.next = p.next
                p.next = temp
        return pre.next
```

# 148. 排序链表
在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序。

示例 1:

输入: 4->2->1->3
输出: 1->2->3->4
示例 2:

输入: -1->5->3->4->0
输出: -1->0->3->4->5

## 方法
这个题要求用O(nlongn)的时间复杂度和O(1)的空间复杂度。所以可以使用merge排序，但是如果是链表可以修改指针，把两个有序链表进行原地的合并。

Merge排序就是先划分成一前一后等分的两块，然后对两块分别进行排序，然后再合并两个有序序列。

第一步，如何等分地划分，可以使用快慢指针的方式，当快指针到达结尾，那么慢指针到了中间位置，把链表进行截断分成了两个。

第二步，合并有序的序列，对于单链表来说，正好用到了 [Merge Two Sorted Lists](https://cathy3.github.io/2019/02/20/%E7%94%A8python%E5%88%B7LeetCode%E4%B8%AD%E9%93%BE%E8%A1%A8%E9%A2%98(%E7%AE%80%E5%8D%95%E7%BA%A7)/) 里的把两个链表合并的方法。

事实上，这个答案里面并不是O(1)的空间，因为，第一，添加了新的链表头的个数会随着递归的次数而不断增加，并不是常量个；第二，递归本身就不是常量空间。

```python
class Solution(object):
    def sortList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        # 划分成两部分
        if not head or not head.next: return head
        pre, fast, slow = head, head, head
        while fast and fast.next: # 找中间位置
            pre = slow
            slow = slow.next
            fast = fast.next.next
        pre.next = None # 前半部分结尾空
        
        L1 = self.sortList(head) # 递归划分
        L2 = self.sortList(slow)
        return self.merge2Lists(L1, L2)
    
    # 合并两个有序数组
    def merge2Lists(self, l1, l2):
        head = ListNode(0)
        move = head
        if not l1: return l2
        if not l2: return l1
        while l1 and l2:
            if l1.val <= l2.val:
                move.next = l1
                l1 = l1.next
            else:
                move.next = l2
                l2 = l2.next
            move = move.next
        move.next = l2 if l2 else l1
        return head.next
```

# 参考
-   [LeetCode 2. Add Two Numbers 解题报告（Python & C++）](https://blog.csdn.net/fuxuemingzhu/article/details/79379626)