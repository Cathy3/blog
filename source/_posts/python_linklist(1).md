---
title: python解LeetCode链表(1)
date: 2019-02-28
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 链表
---
链表是数据结构之一，其中的数据呈线性排列。

链表在前一结点里用链接的方式显示地记录与下一结点的关系。不同于数组，链表的元素的物理地址可以任意。

-   优点：插入、删除操作方便，不会导致元素的移动，因为元素增减，只需要调整指针。 空间大小可以随意扩大
-   缺点：只能通过顺次指针访问，查找不方便

学习目标：
-   实现单链表、循环链表、双向链表，支持增删操作。
-   leetcode 206. 反转链表

<!-- more -->


# 实现单链表
只需要掌握这个表的首结点，从它出发可以找到这个表里的下一结点，以至于找到表里的所有数据元素。
```python
class Node: # 链表的结点
    def __init__(self, x, next_=None):
        self.val = x
        self.next = next_

class SingleLinkList: # 单链表
    def __init__(self): # 空表
        self.head = None
    
    #增
    def insertAsFirst(self, x): #x在首位插入
        node = Node(x)
        node.next = self.head
        self.head = node
            
    def insertAsLast(self, x): #x在尾部插入
        if not self.head:
            self.head = Node(x)
            return
        cur = self.head
        while cur.next != None:
            cur = cur.next
        node = Node(x)
        cur.next = node
    
    #删
    def remove(self, p):#删除在位置p处的结点，返回其数值
        k = self.head
        for i in range(p-1):
            k = k.next
        x = k.next.val
        k.next = k.next.next
        return x
    
    def remove_last(self): #删除表尾的结点
        p = self.head
        if p.next is None:
            x = p.val
            self.head = None
            return x
        while p.next.next is not None:  # 直到 p.next 是最后结点
            p = p.next
        x = p.next.val
        p.next = None
        return x

    def show(self):
        cur = self.head
        while cur != None:
            # cur是一个有效的节点
            print(cur.val, end=' --> ')
            cur = cur.next
        print()
    
if __name__ == '__main__':
    l = SingleLinkList()
    for i in range(10):
        l.insertAsLast(i)
    l.show()
    l.remove_last()
    l.show()
```
输出：
```
0 --> 1 --> 2 --> 3 --> 4 --> 5 --> 6 --> 7 --> 8 --> 9 --> 
```
# 双向链表
增加了反向的链接，结点操作更加方便。但是每个结点都需要增加一个链接域，增加的空间开销与结点数成正比，`O(n)`
```python
class DLNode: # 链表的结点
    def __init__(self, x, prev=None, next_=None):
        self.val = x
        self.next = next_
        self.prev = prev

class DoubleLinkList: # 双链表
    def __init__(self): # 空表
        self.head = None
    
    #增
    def insertAsFirst(self, x): #x在首位插入               
        if not self.head:
            self.head = DLNode(x)
        else:
            node = DLNode(x,None,self.head)
            self.head.prev = node 
            self.head = node   # node 作为新的头结点
            
    def insertAsLast(self, x): #x在尾部插入
        if not self.head:
            self.head = DLNode(x)
        else:
            cur = self.head
            while cur.next != None: # 移动到链表尾部
                cur = cur.next
            node = DLNode(x)
            cur.next = node
            node.prev = cur
    
    #删
    def remove(self, p):#删除在位置p处的结点，返回其数值
        k = self.head
        for i in range(p-1):
            k = k.next
        x = k.next.val
        k.next = k.next.next
        k.next.prev = k
        return x
    

    def show(self):
        cur = self.head
        while cur != None:
            # cur是一个有效的节点
            print(cur.val, end=' -- ')
            cur = cur.next
        print()
    def show_reverse(self):
        cur = self.head
        while cur.next != None:
            cur = cur.next
        while cur != None:
            print(cur.val, end=' -- ')
            cur = cur.prev
        print()    
    
if __name__ == '__main__':
    l = DoubleLinkList()
    for i in range(10):
        l.insertAsLast(i)
    l.show()
    l.remove(2)
    l.show()
    l.show_reverse()
```
输出：
```
0 -- 1 -- 2 -- 3 -- 4 -- 5 -- 6 -- 7 -- 8 -- 9 -- 
0 -- 1 -- 3 -- 4 -- 5 -- 6 -- 7 -- 8 -- 9 -- 
9 -- 8 -- 7 -- 6 -- 5 -- 4 -- 3 -- 1 -- 0 --
```


# Leetcode 206.反转链表
[Reverse Linked List](https://leetcode-cn.com/problems/reverse-linked-list/)

反转一个单链表。

**示例:**

> 输入: 1->2->3->4->5->NULL

>输出: 5->4->3->2->1->NULL
### 方法：两个指针，头插法
- 用两个指针，p指针记录的是每次的队头元素，q指针指向下一个要插入队头的元素。
- head帮忙指向下轮要头插的元素，待本轮指针翻转完之后要把该元素赋给q

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