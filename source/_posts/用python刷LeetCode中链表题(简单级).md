---
title: 用python刷LeetCode链表题(简单级)
date: 2019-02-20
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 链表
---
**练习题目：**

-   21. 合并两个有序链表
-   83. 删除排序链表中的重复元素
-   141. 环形链表
-   160. 相交链表
-   203. 移除链表元素
-   206. 反转链表
-   234. 回文链表
-   237. 删除链表中的节点

<!-- more -->

# 21. 合并两个有序链表
[Merge Two Sorted Lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

## 题目描述
将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 


**示例：**

> 输入：1->2->4, 1->3->4

> 输出：1->1->2->3->4->4


## 方法
同时遍历两个链表，每次选取两个链表中较小值的节点，依次连接起来，就能得到最终的链表。

注意：
1. 返回值要返回head.next
2. 无需判断循环后哪个不为空，or返回第一个为真的值

``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def mergeTwoLists(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        head = cur = ListNode(0)
        
        while l1 and l2:
            if l1.val < l2.val:
                cur.next = l1
                l1 = l1.next
            else:
                cur.next = l2
                l2 = l2.next
            cur = cur.next
        cur.next = l1 or l2
        return head.next
```
# 83. 删除排序链表中的重复元素
[Remove Duplicates from Sorted List](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

## 题目描述
给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

**示例 1:**

>输入: 1->1->2

>输出: 1->2

**示例 2:**

>输入: 1->1->2->3->3

>输出: 1->2->3

## 方法一：两个指针
用两个指针来完成，分别记录要删除的链表元素和上一个元素。
``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteDuplicates(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head:
            return head
        p = head
        q = head.next
        while q:
            if p.val == q.val:
                p.next = q.next
                q = q.next
            else:
                p = p.next
                q = q.next
        return head
```

## 方法二： 一个指针
```python
class Solution:
    def deleteDuplicates(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        cur = head
        
        while cur:
            while cur.next and cur.val == cur.next.val:
                cur.next = cur.next.next
            cur = cur.next
        return head
```

# 141. 环形链表
[Linked List Cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

## 题目描述
给定一个链表，判断链表中是否有环。

## 方法：快慢双指针
-   通过使用具有**不同速度**的快、慢两个指针遍历链表，空间复杂度可以被降低至 `O(1)`。慢指针每次移动一步，而快指针每次移动两步。
-   如果列表中不存在环，最终快指针将会最先到达尾部，此时我们可以返回 false。
-   考虑一个环形链表，把慢指针和快指针想象成两个在环形赛道上跑步的运动员（分别称之为walker与runner）。而runner最终一定会追上walker。这是为什么呢？考虑下面这种情况（记作情况 A） - 假如runner只落后walker一步，在下一次迭代中，它们就会分别跑了一步或两步并相遇。
-   其他情况又会怎样呢？例如，我们没有考虑快跑者在慢跑者之后两步或三步的情况。但其实不难想到，因为在下一次或者下下次迭代后，又会变成上面提到的情况 A。

```python
class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        if not head:
            return False
        walker = head
        runner = head.next
        try:
            while walker != runner:
                walker = walker.next
                runner = runner.next.next
            return True
        except:
            return False
```
略微改变语法形式，思路没有区别：

```python
class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        if not head or not head.next :
            return False
        walker = head
        runner = head.next
        while walker != runner:  
            # 改成判断快指针先到达尾部
            if not runner or not runner.next:
                return False 
            walker = walker.next
            runner = runner.next.next
        return True
```
更简洁的写法

```python
class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        fast = slow = head
        while slow and fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow is fast:
                return True
        return False
```

# 160. 相交链表
[Intersection of Two Linked Lists](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/solution/)

## 题目描述
编写一个程序，找到两个单链表相交的起始节点。

如下面的两个链表：

![avatar](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

在节点 c1 开始相交。

注意：

-   如果两个链表没有交点，返回 null.
-   在返回结果后，两个链表仍须保持原有的结构。
-   可假定整个链表结构中没有循环。
-   程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存。

## 方法：两指针
-   判断链表是否有交集，可以设置两个指针，一个指针从第一个链表开始遍历，遍历完第一个链表再遍历第二个链表，另一个指针从第二个链表开始遍历，遍历完第二个链表再遍历第一个链表.
-   不管两个链表在交集前的长度如何，遍历两个链表长度总和相同。
-   如果有交集的话，两个指针肯定会同时遍历到最后的交集部分。

```python
class Solution(object):
    def getIntersectionNode(self, headA, headB):
        """
        :type head1, head1: ListNode
        :rtype: ListNode
        """
        if not headA or not headB:
            return None
        pa = headA
        pb = headB
        while pa is not pb:
            pa = headB if pa == None else pa.next
            pb = headA if pb == None else pb.next
        return pa
```

# 203. 移除链表元素

## 题目描述
删除链表中等于给定值 val 的所有节点。

**示例:**

>输入: 1->2->6->3->4->5->6, val = 6

>输出: 1->2->3->4->5

## 方法：dummy node
- 有了第83题的思路，我们这里可以用一个指针来进行链表的遍历，
- 但是这里需要注意的是，头节点也需要进行判断，如果头节点的值等于val的话，我们不能返回头节点，所以这里很巧妙的重新生成了一个无关的头节点dummy node。

```python
class Solution(object):
    def removeElements(self, head, val):
        """
        :type head: ListNode
        :type val: int
        :rtype: ListNode
        """
        dummy = ListNode(-1)
        dummy.next = head
        cur = dummy
        while cur:
            while cur.next and cur.next.val == val:
                cur.next = cur.next.next
            cur = cur.next
        return dummy.next
```

# 206. 反转链表
[Reverse Linked List](https://leetcode-cn.com/problems/reverse-linked-list/)

## 题目描述
反转一个单链表。

**示例:**

> 输入: 1->2->3->4->5->NULL

>输出: 5->4->3->2->1->NULL

## 方法：两个指针，头插法
-   用两个指针，p指针记录的是每次的队头元素，q指针指向下一个要插入队头的元素。
-   head帮忙指向下轮要头插的元素，待本轮指针翻转完之后要把该元素赋给q

```python
class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head:
            return None
        p = head  # p是指向每次的队头元素
        q = head.next  # q是指下一个要插入队头的元素
        while q:
            head.next = q.next # 保留后一个要插入的元素地址
            q.next = p # 指针反转，next为队头元素
            p = q #q插入，作为新的队头
            q = head.next # 重新指定下一个要插入队头的元素
        return p
```

## 较简洁的写法

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        cur, prev = head, None
        while cur:
            cur.next, prev, cur = prev, cur, cur.next
        return prev
```

# 234. 回文链表
[Palindrome Linked List](https://leetcode-cn.com/problems/palindrome-linked-list/)

## 题目描述
请判断一个链表是否为回文链表。

**示例 1:**

>输入: 1->2

>输出: false

**示例 2:**

>输入: 1->2->2->1

>输出: true

## 方法：两个指针
两个指针，一个指针从前往后走，一个指针从后往前走，判断元素值是否相同，这里要分几个步骤来进行求解：
1、找到链表长度的一半，用追赶法，一个指针一次走两步，一个指针一次走一步
2、将后一半数组反转
3、比较两边元素是否相同，判断链表是否是回文链表
```python
class Solution(object):
    def isPalindrome(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        slow = fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
        
        node = None #反转后一半链表
        while slow:
            nxt = slow.next 
            slow.next = node 
            node = slow # node是反转后的第一个元素
            slow = nxt 
            
        while node and head:
            if node.val != head.val:
                return False
            node = node.next
            head = head.next
        return True
```

# 237. 删除链表中的节点
[Delete Node in a Linked List](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/) 

## 题目描述
输入：要求被删除的（非末尾）节点

函数功能：删除某个链表中的这个节点。无返回。

**示例 1:**

>输入: head = [4,5,1,9], node = 5

>输出: [4,1,9]

>解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.

**示例 2:**

>输入: head = [4,5,1,9], node = 1

>输出: [4,5,9]

>解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
 

**说明:**

- 链表至少包含两个节点。
- 链表中所有节点的值都是唯一的。
- 给定的节点为非末尾节点并且一定是链表中的一个有效节点。
- 不要从你的函数中返回任何结果。

## 方法:替换值
- 关键是理解题意，不给你整个链表，只给你一个节点，如何把这个节点删除。
- 其实我们没必要真的把这个节点删除，而是把这个节点对应的val值删除即可，
- 所以我们可以偷天换日，把下一个节点的值赋给这个节点，再把下一个节点删除。

```python 
class Solution(object):
    def deleteNode(self, node):
        """
        :type node: ListNode
        :rtype: void Do not return anything, modify node in-place instead.
        """
        node.val = node.next.val
        node.next = node.next.next
```
# 参考
-   [3个月用python刷完leetcode600题!-linked_list简单题](https://ask.hellobi.com/blog/wenwen/8653)
-   [21. Merge Two Sorted Lists [easy] (Python)](https://blog.csdn.net/coder_orz/article/details/51529359)