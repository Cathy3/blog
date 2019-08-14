---
title: python解LeetCode链表(3)
date: 2019-04-05
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 链表
---
转换链表节点的位置 （穿针引线）

-   206. 反转链表
-   92. 反转链表 II
-   24. 两两交换链表中的节点
-   25. K 个一组翻转链表
-   61. 旋转链表
-   86. 分隔链表
-   143. 重排链表
-   排序
    -   147. 对链表进行插入排序
    -   148. 排序链表（归并排序）
-   328. 奇偶链表


<!-- more -->

# 206.反转链表
[Reverse Linked List](https://leetcode-cn.com/problems/reverse-linked-list/)

反转一个单链表。

**示例:**
```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

## 方法一：两个指针，头插法
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

## 方法二

```python
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        cur, prev = head, None
        while cur:
            # 链表反转，更新指针pre，更新指针cur
            cur.next, prev, cur = prev, cur, cur.next
        return prev
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
翻转链表的而变形题目了。进行一遍遍历，把第m到n个元素进行翻转，即依次插入到第m个节点的头部。

实际面试中细节考虑：m和n超过链表范围怎么办？ m>n怎么办？
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

# 24. 两两交换链表中的节点
[Swap Nodes in Pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例: 给定 1->2->3->4, 你应该返回 2->1->4->3.

## 方法
a, b, pre 三个指针变量

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
            a = pre.next # pre是每次要调换的两个元素的前一个元素
            b = a.next # a,b是要调换的相邻元素
            pre.next, b.next, a.next =b, a, b.next # next连线重置
            pre = a 
        return self.next
```

# 25. K 个一组翻转链表
给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

示例 :
```
给定这个链表：1->2->3->4->5
当 k = 2 时，应当返回: 2->1->4->3->5
当 k = 3 时，应当返回: 3->2->1->4->5
```
说明 :

-   你的算法只能使用常数的额外空间。
-   你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

## 方法
翻转函数单独写出来调用更清晰。每次k个元素翻转，再和前后两个节点连接起来。
```python
class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        if not head or k <= 1: 
            return head
        dummy = ListNode(-1) #设立链表的虚拟头结点
        dummy.next = head
        temp = dummy # 遍历整个链表
        while temp:
            temp = self.reverseNextK(temp, k)
        return dummy.next
    
    
    def reverseNextK(self, head, k):        
        # 检查当前遍历剩余节点数是否比k小
        temp = head
        for _ in range(k):
            if not temp.next:
                return None
            temp = temp.next
            
        prev, cur, tail = head, head.next, head.next
        for _ in range(k):
            # 链表反转，更新指针pre，更新指针cur
            cur.next, prev, cur = prev, cur, cur.next
        # 连接头head和尾tail
        head.next, tail.next = prev, cur
        return tail
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

这个题类似于快排的过程。

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

# 143. 重排链表
给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

示例1:给定链表 1->2->3->4, 重新排列为 1->4->2->3.

示例 2:
```
给定链表 1->2->3->4->5, 重新排列为 1->5->2->4->3.
```
## 方法
题目大意:把一个链表的前半部分正序，后半部分逆序，然后一个一个的连接起来。

链表无法随机访问数据，如何获得中间的元素？ 奇数还是偶数个节点？

两次遍历还是一次遍历？
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

# 328.奇偶链表

## 方法
给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。

请尝试使用原地算法完成。你的算法的空间复杂度应为 O(1)，时间复杂度应为 O(nodes)，nodes 为节点总数。

示例 1:
```
输入: 1->2->3->4->5->NULL
输出: 1->3->5->2->4->NULL
```
示例 2:
```
输入: 2->1->3->5->6->4->7->NULL 
输出: 2->3->6->7->1->5->4->NULL
```
说明:

-   应当保持奇数节点和偶数节点的相对顺序。
-   链表的第一个节点视为奇数节点，第二个节点视为偶数节点，以此类推。

## 方法
两条链,如果是奇数节点放到奇链，如果是偶数节点就放到偶链。最后，把偶链放到奇链的后面就好了。

注意，偶链的末尾指针要设置成空，已让单链表终止。

比如对于用例[1,2,3]，奇数链是1->3，偶链是2，而遍历完成后的偶链2仍然指向3的，所以死循环了。把尾指针设置成空就能终止了。

```python
class Solution:
    def oddEvenList(self, head: ListNode) -> ListNode:
        odd = ListNode(0)
        even = ListNode(0)
        oddHead, evenHead = odd, even
        index = 0
        while head:
            if index & 1 == 0:
                odd.next = head
                odd = odd.next
            else:
                even.next = head
                even = even.next
            head = head.next
            index += 1
        even.next = None
        odd.next = evenHead.next
        return oddHead.next
```



# 参考
-   [LeetCode 2. Add Two Numbers 解题报告（Python & C++）](https://blog.csdn.net/fuxuemingzhu/article/details/79379626)
-   [【LeetCode】142. Linked List Cycle II 解题报告（Python & C++）](https://blog.csdn.net/fuxuemingzhu/article/details/79530638)