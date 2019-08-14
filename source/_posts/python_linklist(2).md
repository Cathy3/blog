---
title: python解LeetCode链表(2)
date: 2019-02-20
categories: 
		- Algorithm
tags:  
        - pythongit
        - LeetCode
        - 链表
---
**练习题目：**

-   合并有序链表
    -   21. 合并两个有序链表
    -   23. 合并K个排序链表
-   删除节点
    -   19. 删除链表的倒数第N个节点
    -   83. 删除排序链表中的重复元素
    -   82. 删除排序链表中的重复元素 II
    -   203. 移除链表元素
    -   237. 删除链表中的节点
-   141. 环形链表
-   142. [环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
-   160. 相交链表
-   234. 回文链表

<!-- more -->

# 21. 合并两个有序链表
[Merge Two Sorted Lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 


**示例：**
```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

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
# 23.合并K个排序链表
合并 k 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。

示例:
```
输入:
[
  1->4->5,
  1->3->4,
  2->6
]
输出: 1->1->2->3->4->4->5->6
```

## 方法
把问题简化为merge2的问题，这种思路可以每次递归值归并前两个，或是或是每次把相邻的两个都归并了，后者的栈的深度要更小。
```python
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        if not lists: return
        amount = len(lists)
        interval = 1
        while interval < amount:
            for i in range(0, amount - interval, interval * 2):
                lists[i] = self.merge2Lists(lists[i], lists[i + interval])
            interval *= 2
        return lists[0]

    def merge2Lists(self, l1, l2):
        head = point = ListNode(0)
        while l1 and l2:
            if l1.val <= l2.val:
                point.next = l1
                l1 = l1.next
            else:
                point.next = l2
                l2 = l1
                l1 = point.next.next
            point = point.next
        if not l1:
            point.next=l2
        else:
            point.next=l1
        return head.next
```

# 19. 删除链表的倒数第N个节点
给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

示例：
```
给定一个链表: 1->2->3->4->5, 和 n = 2.
当删除了倒数第二个节点后，链表变为 1->2->3->5.
```
说明：给定的 n 保证是有效的。

## 方法：快慢指针
实际面试中考虑：n从0计还是从1计; n不合法，负数或者大于链表长度如何处理（保证n合法）

用两个指针（只需要遍历一次），一个快指针从前到后扫描整个链表，另一个慢指针的步数差n+1，那么当p指向尾部的Null时，指针q恰好指向要删除节点的前一个节点。

由于可能删除头部节点，伪装一个新的头部方便操作。

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummyHead = ListNode(0) # 可能删除了头结点
        dummyHead.next = head
        fast = slow = dummyHead
        for i in range(n+1):
            fast = fast.next
        while fast:
            fast = fast.next
            slow = slow.next
        slow.next = slow.next.next # 删除第n个
        return dummyHead.next
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

# 82. 删除排序链表中的重复元素 II
给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现的数字。

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
## 方法：dummy node
注意审题啊，这个distinct的意思并不是去重，而是删除出现次数不止一次的。

去重的可以看这个题：83. Remove Duplicates from Sorted List

因此必须先遍历一遍，统计每个节点出现的次数。

第二次遍历的时候，查找下个节点的值出现的次数如果不是1次，那么就删除下个节点。修改这个节点的下个指针指向下下个节点，这是指向该节点位置的指针不要动，因为还要判断新的next值。

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        dummy = ListNode(0) # 设立链表的虚拟头结点
        dummy.next = head
        valist = []
        while head:
            valist.append(head.val)
            head = head.next
        counter = collections.Counter(valist)
        
        head = dummy
        while head and head.next:
            if counter[head.next.val] != 1:
                head.next = head.next.next
            else:
                head = head.next
        return dummy.next
```

# 203. 移除链表元素

## 题目描述
删除链表中等于给定值 val 的所有节点。

**示例:**

>输入: 1->2->6->3->4->5->6, val = 6

>输出: 1->2->3->4->5

## 方法：dummy node
- 有了第83题的思路，我们这里可以用一个指针来进行链表的遍历，
- 注意：头节点也需要进行判断，如果头节点的值等于val的话，我们不能返回头节点，所以这里很巧妙的重新生成了一个无关的头节点dummy node。（设立链表的虚拟头结点）

```python
class Solution(object):
    def removeElements(self, head, val):
        """
        :type head: ListNode
        :type val: int
        :rtype: ListNode
        """
        dummy = ListNode(-1) #设立链表的虚拟头结点
        dummy.next = head
        cur = dummy
        while cur:
            while cur.next and cur.next.val == val:
                cur.next = cur.next.next
            cur = cur.next
        return dummy.next
```

# 237. 删除链表中的节点
[Delete Node in a Linked List](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/) 

输入：要求被删除的（非末尾）节点

函数功能：删除某个链表中的这个节点。无返回。

**示例 1:**
```
输入: head = [4,5,1,9], node = 5
输出: [4,1,9]
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
```
**示例 2:**
```
输入: head = [4,5,1,9], node = 1
输出: [4,5,9]
解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
```

**说明:**

-   链表至少包含两个节点。
-   链表中所有节点的值都是唯一的。
-   给定的节点为非末尾节点并且一定是链表中的一个有效节点。
-   不要从你的函数中返回任何结果。

## 方法:替换值
-   关键是理解题意，不给你整个链表，只给你一个节点，如何把这个节点删除。
-   其实没必要真的把这个节点删除，而是把这个节点对应的val值删除即可，
-   所以可以偷天换日，把下一个节点的值赋给这个节点，再把下一个节点删除。

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

# 141. 环形链表
[Linked List Cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

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

# 160. 相交链表
[Intersection of Two Linked Lists](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/solution/)

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


# 234. 回文链表
[Palindrome Linked List](https://leetcode-cn.com/problems/palindrome-linked-list/)

请判断一个链表是否为回文链表。

**示例 1:**
```
输入: 1->2
输出: false
```
**示例 2:**
```
输入: 1->2->2->1
输出: true
```

## 方法：两个指针
两个指针，一个指针从前往后走，一个指针从后往前走，判断元素值是否相同，这里要分几个步骤来进行求解：

1.  找到链表长度的一半，用追赶法，一个指针一次走两步，一个指针一次走一步
2.  将后一半数组反转
3.  比较两边元素是否相同，判断链表是否是回文链表

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

# 参考
-   [3个月用python刷完leetcode600题!-linked_list简单题](https://ask.hellobi.com/blog/wenwen/8653)
-   [21. Merge Two Sorted Lists [easy] (Python)](https://blog.csdn.net/coder_orz/article/details/51529359)