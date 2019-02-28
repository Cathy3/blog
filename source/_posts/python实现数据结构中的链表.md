---
title: python实现数据结构中的链表
date: 2019-02-28
categories: 
		- Algorithm
tags:  
        - python
        - LeetCode
        - 链表
---
实现单链表、循环链表、双向链表，支持增删操作

# 实现单链表
```python
class Node:
    def __init__(self, x):
        self.val = x
        self.next = None

class SingleLinkList:
    def __init__(self):
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
    def remove(self, p):#初除位置p处的节点，返回其数值
        x = p.val
        k = self.head
        for i in range(p-1):
            k = k.next
        k.next = k.next.next
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
```
输出：
```
0 --> 1 --> 2 --> 3 --> 4 --> 5 --> 6 --> 7 --> 8 --> 9 --> 
```

# Leetcode习题 
## 206. 反转链表
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