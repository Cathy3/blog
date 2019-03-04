---
title: python实现数据结构中的队列
date: 2019-03-01
categories: 
	- Algorithm
tags:  
        - python
        - LeetCode
        - 数据结构
        - 队列
mathjax: true
---
队列中的数据呈线性排列。与栈类似，队列中可以操作数据的位置也有一定的限制。在栈中，数据的添加和删除都在同一端进行，而在队列中则分别是在两端进行的。队列也不能直接访问位于中间的数据，必须通过出队操作将目标数据变成首位后才能访问。

像队列这种最先进去的数据最先被取来，即“先进先出”的结构，我们称为 First In First Out，简称 FIFO。 

“先来的数据先处理”是一种很常见的思路，所以队列的应用范围非常广泛。比如广度优先搜索算法，通常就会从搜索候补中选择最早的数据作为下一个顶点。此时，在候补顶点的管理上就可以使用队列。

学习目标：
- 用数组实现一个顺序队列
- 用链表实现一个链式队列
- 实现一个循环队列
- LeetCode 641. 设计循环双端队列
- LeetCode 239. 滑动窗口最大值

<!-- more -->

# 用数组实现一个顺序队列
list表头入队，表尾出队。反过来也可以。

实现的基本操作有：
- enqueue 入队，要把表中其余元素全部后移，再插入首元素，需要 $O(n)$时间
- dequeue 出队,$O(1)$

```python
class Queue:
    def __init__(self):
        self.items = []
    def enqueue(self, item): # 入队
        self.items.insert(0,item)
    def dequeue(self): # 出队
        return self.items.pop()
    
if __name__ == '__main__':
    q = Queue()
    q.enqueue(4)
    q.enqueue(5)
    q.enqueue(6)
    print(q.dequeue())
```
输出：
```
4
```

# 用链表实现一个链式队列
等同于用带表尾指针的单链表。链尾入队，链首出队。

实现的基本操作有：
- enqueue 入队   $O(1)$
- dequeue 出队   $O(1)$
- peek 查看队首元素 $O(1)$

```python
class Node: # 链表的结点
    def __init__(self, x, next_=None):
        self.val = x
        self.next = next_

class LQueue: # 
    def __init__(self): # 空表
        self._head = None
        self._rear = None # 尾结点
    
    def enqueue(self, x): # 入队
        if self._head is None:
            self._head = Node(x, self._head)
            self._rear = self._head
        else:
            self._rear.next = Node(x)
            self._rear = self._rear.next
    def dequeue(self): # 出队
        if self._head is None:
            return 
        x = self._head.val
        self._head = self._head.next
        return x
    def peek(self): # 查看最早入队的元素
        if self._head is None:
            return
        return self._head.val 
    
if __name__ == '__main__':
    l = LQueue()
    l.enqueue(4)
    l.enqueue(5)
    print(l.peek())
    l.enqueue(6)
    l.dequeue()
    print(l.peek())
```
输出：
```
4
5
```

# 实现一个循环队列
[LeetCode 622](https://leetcode-cn.com/problems/design-circular-queue/)

队头变量`_head`记录当前队列的第一个元素位置
```python
class QueueUnderflow(ValueError):# 空队列无法 dequeue的异常错误
    pass

class SQueue:
    def __init__(self, init_len=8):
        self._elems = [0]*init_len  # 队列元素
        self._head = 0 # 队列首元素位置的下标
        self._num = 0  # 表中元素个数
        self._len = init_len   # 当前表的长度，表满了的话，要换一个存储表
    def peek(self):
        if self._num == 0: # 队空状态
            raise QueueUnderflow
        return self._elems[self._head]
    def dequeue(self):
        if self._num == 0:  # 队空状态
            raise QueueUnderflow
        e = self._elems[self._head]
        self._head = (self._head+1) % self._len 
        self._num -= 1
        return e
    def enqueue(self, e):
        if self._num == self._len: # 队满状态
            self.__extend() # 扩大存储区
        # 新元素的入队位置
        self._elems[(self._head + self._num) % self._len]=e
        self._num += 1
    def __extend(self):  # 扩容
        old_len = self._len
        self._len *= 2
        new_elems = [0] * self._len
        for i in range(old_len):
            new_elems[i] = self._elems[(self._head + i)%old_len]
        self._elems, self._head = new_elems, 0
        
if __name__ == '__main__':
    l = SQueue()
    for i in range(10):
        l.enqueue(i+1)
    print(l.peek())
    l.dequeue()
    print(l.peek())
```

# LeetCode 641. 设计循环双端队列
[Design Circular Deque](https://leetcode-cn.com/problems/design-circular-deque/)
设计实现双端队列。
你的实现需要支持以下操作：

- MyCircularDeque(k)：构造函数,双端队列的大小为k。
- insertFront()：将一个元素添加到双端队列头部。 如果操作成功返回 true。
- insertLast()：将一个元素添加到双端队列尾部。如果操作成功返回 true。
- deleteFront()：从双端队列头部删除一个元素。 如果操作成功返回 true。
- deleteLast()：从双端队列尾部删除一个元素。如果操作成功返回 true。
- getFront()：从双端队列头部获得一个元素。如果双端队列为空，返回 -1。
- getRear()：获得双端队列的最后一个元素。 如果双端队列为空，返回 -1。
- isEmpty()：检查双端队列是否为空。
- isFull()：检查双端队列是否满了。

示例：
```
MyCircularDeque circularDeque = new MycircularDeque(3); // 设置容量大小为3
circularDeque.insertLast(1);			        // 返回 true
circularDeque.insertLast(2);			        // 返回 true
circularDeque.insertFront(3);			        // 返回 true
circularDeque.insertFront(4);			        // 已经满了，返回 false
circularDeque.getRear();  				// 返回 2
circularDeque.isFull();				        // 返回 true
circularDeque.deleteLast();			        // 返回 true
circularDeque.insertFront(4);			        // 返回 true
circularDeque.getFront();				// 返回 4
 ```
 

提示：

- 所有值的范围为 [1, 1000]
- 操作次数的范围为 [1, 1000]
- 请不要使用内置的双端队列库。

## 方法
和上一题思路相同，用一个list实现类似的环形列表。
双向链表可以从头尾插入元素，对应了list的insert和append方法。注意，无论是在头尾插入，要移动的指针都是rear。

```python
class MyCircularDeque:

    def __init__(self, k: int):
        """
        Initialize your data structure here. Set the size of the deque to be k.
        """
        self._elems = [0]*k  # 队列元素
        self._head = 0 # 队列首元素位置的下标
        self._num = 0  # 表中元素个数
        self._len = k   # 表的长度

    def insertFront(self, value: int) -> bool:
        """
        Adds an item at the front of Deque. Return true if the operation is successful.
        """
        if self._num == self._len: # 队满状态
            return False
        # 新元素的入队位置
        self._head = (self._head + self._len - 1) % self._len
        self._elems[self._head]= value
        self._num += 1
        return True

    def insertLast(self, value: int) -> bool:
        """
        Adds an item at the rear of Deque. Return true if the operation is successful.
        """
        if self._num == self._len: # 队满状态
            return False
        # 新元素的入队位置
        self._elems[(self._head + self._num) % self._len]= value
        self._num += 1
        return True
        

    def deleteFront(self) -> bool:
        """
        Deletes an item from the front of Deque. Return true if the operation is successful.
        """
        if self._num == 0:  # 队空状态
            return False
        # e = self._elems[self._head]
        self._head = (self._head+1) % self._len 
        self._num -= 1
        return True

    def deleteLast(self) -> bool:
        """
        Deletes an item from the rear of Deque. Return true if the operation is successful.
        """
        if self._num == 0:  # 队空状态
            return False
        # e = self._elems[(self._head + self._num-1) % self._len]
        self._num -= 1
        return True

    def getFront(self) -> int:
        """
        Get the front item from the deque.
        """
        if self._num == 0: # 队空状态
            return -1
        return self._elems[self._head]

    def getRear(self) -> int:
        """
        Get the last item from the deque.
        """
        if self._num == 0: # 队空状态
            return -1
        return self._elems[(self._head + self._num-1) % self._len]

    def isEmpty(self) -> bool:
        """
        Checks whether the circular deque is empty or not.
        """
        return  self._num == 0

    def isFull(self) -> bool:
        """
        Checks whether the circular deque is full or not.
        """
        return  self._len == self._num


# Your MyCircularDeque object will be instantiated and called as such:
# obj = MyCircularDeque(k)
# param_1 = obj.insertFront(value)
# param_2 = obj.insertLast(value)
# param_3 = obj.deleteFront()
# param_4 = obj.deleteLast()
# param_5 = obj.getFront()
# param_6 = obj.getRear()
# param_7 = obj.isEmpty()
# param_8 = obj.isFull()
```


# LeetCode 239. 滑动窗口最大值
[Sliding Window Maximum](https://leetcode-cn.com/problems/sliding-window-maximum/)


## 方法：deque（双端队列）

遍历数组nums，使用双端队列deque维护滑动窗口内有可能成为最大值元素的数组下标。

当下标i从队尾入队时，顺次弹出队列尾值`<=nums[i]`的数组下标。

当前下标为i，则滑动窗口的有效下标范围为[i - (k - 1), i] ,所以当队头元素`dq[0] == i-k`，就要从队头出队。

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        dq = collections.deque() # 队列
        res = []
        for i in range(len(nums)):
            while dq and nums[dq[-1]] <= nums[i]:#(循环)当dq队尾值小于新元素nums[i]
                dq.pop() #队尾值出队(直到dq中的值都大于新元素nums[i])
            dq.append(i) #新元素入队
            if dq[0] == i-k: # 窗口滑出，队头元素不在窗口区域内
                dq.popleft() # 队头值出队
            if i >= k-1: # i遍历到一个窗口大小之后，每轮都能执行此行。
                res.append(nums[dq[0]]) #窗口最大元素
        return res
```

# 参考
- 《我的第一本算法书》
- [[LeetCode]Sliding Window Maximum ](http://bookshadow.com/weblog/2015/07/18/leetcode-sliding-window-maximum/)