---
title: python实现数据结构之哈希表
date: 2019-03-07
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   哈希表
---
散列表（Hash table，也叫哈希表），是根据键（Key）而直接访问在内存存储位置的数据结构。 

具体方法是：

1.  选定一个整数的下标范围（通常以 0 或 1 开始)，建立一个包括相应元素位置范围的顺序表。
2.  选定一个从实际关键码集合到上述下标范围的适当映射 h 
    -   在需要存入关键码为 key 的数据时，将其存入表中第 h(key) 个位置。
    -   遇到以 key 为关键码检索数据时，直接去找表中第 h(key) 个位置的元素。

这个 h 称为散列函数， 也常被称为哈希（hash)函数或杂凑函数，它就是从可能的关键码集合到一个整数区间（下标区间）的映射。

学习目标： 

-   实现一个基于链表法解决冲突问题的散列表
-   实现一个 LRU 缓存淘汰算法 
-   LeetCode 1. 两数之和
-   LeetCode 202. Happy Number

<!-- more -->

# 实现一个基于链表法解决冲突问题的散列表
在存储数据的过程中，如果发生冲突，可以利用链表在已有数据的后面插入新数据来解决冲突。这种方法被称为“链地址法”。 

在哈希表中，我们可以利用哈希函数快速访问到数组中的目标数据。如果发生哈希冲突，就使用链表进行存储。这样一来，不管数据量为多少，我们都能够灵活应对。 


```python
class HashTable:
    def __init__(self):
        self.size = 11 #容量自定，但必须是素数
        self.hash_table = [[] for _ in range(self.size)] # 存放keys-values
        # list中的list是同一个位置的元素集合
    
    def insert(self,key,value):
        hash_value =hash(key)%self.size  #哈希函数
        key_exists = False 
        bucket = self.hash_table[hash_value]
        for i, kv in enumerate(bucket):
            k, v = kv
            if key == k:
                key_exists = True
                break
        if key_exists:
            bucket[i] = ((key,value))
        else:
            bucket.append((key,value))

    def search(self, key):
        hash_value = hash(key)%self.size  #哈希函数   
        bucket = self.hash_table[hash_value]
        for i, kv in enumerate(bucket):
            k, v = kv
            if key == k:
                return v
    def delete(self, key):
        hash_value = hash(key)%self.size  #哈希函数       
        key_exists = False
        bucket = self.hash_table[hash_value]
        for i, kv in enumerate(bucket):
            k, v = kv 
            if key == k:
                key_exists = True 
                break
        if key_exists:
            del bucket[i]
            return v
        else:
            return 0
 
           
if __name__ == '__main__':           
    H=HashTable()
    H.insert(2,"lion")
    H.insert(10,"cat")
    H.insert(25,"dog")
    print(H.hash_table) # 输出
    H.insert(10,"kitty")
    H.insert(21,"tiger")
    print(H.hash_table) # 输出
    print("查找10的值为"+ H.search(10))
    print("删除10，值为" + H.delete(10))
    print(H.hash_table) # 输出
```
输出：
```
[[], [], [(2, 'lion')], [(25, 'dog')], [], [], [], [], [], [], [(10, 'cat')]]
[[], [], [(2, 'lion')], [(25, 'dog')], [], [], [], [], [], [], [(10, 'kitty'), (21, 'tiger')]]
查找10的值为kitty
删除10，值为kitty
[[], [], [(2, 'lion')], [(25, 'dog')], [], [], [], [], [], [], [(21, 'tiger')]]
```



如果数组的空间太小，使用哈希表的时候就容易发生冲突，线性查找的使用频率也会更高；反过来，如果数组的空间太大，就会出现很多空箱子，造成内存的浪费。因此，给数组设定合适的空间非常重要。


# LeetCode 146. LRU缓存机制
[LRU Cache](https://leetcode-cn.com/problems/lru-cache/)

一个用hash表作为底层结构的数据库，当然少不了缓存淘汰算法。当缓存需要被清理时（比如空间占用已经接近临界值了），需要使用某种淘汰算法来决定清理掉哪些数据。

LRU：Least Recently Used，最近最少使用。判断最近被使用的时间，目前最远的数据优先被淘汰，其核心思想是“如果数据最近被访问过，那么将来被访问的几率也更高”。

LRU 算法过程：

1.  新数据插入到链表头部；
2.  每当缓存命中（即缓存数据被访问），则将数据移到链表头部；
3.  当链表满的时候，将链表尾部的数据丢弃。

这个缓存器主要有两个成员函数，get 和 put。

其中 get 函数是通过输入 key 来获得 value，如果成功获得后，这对 (key, value) 升至缓存器中最常用的位置（顶部），如果 key 不存在，则返回 -1 。

而 put 函数是插入一对新的 (key, value)，如果原缓存器中有该 key，则需要先删除掉原有的，将新的插入到缓存器的顶部。如果不存在，则直接插入到顶部。

若加入新的值后缓存器超过了容量，则需要删掉一个最不常用的值，也就是底部的值。

```python
class LRUCache:

    def __init__(self, capacity: int):
        self.cap = capacity # 容量
        self._cache = [] #缓存
        self._cacheMap = {} #缓存哈希表

    def get(self, key: int) -> int:
        if key not in self._cacheMap:
            return -1
        self._cache.remove(key)
        self._cache.append(key)
        return self._cacheMap[key]

    def put(self, key: int, value: int) -> None:
        if key in self._cacheMap:
            self._cacheMap[key] = value
            self._cache.remove(key)
            self._cache.append(key)
        else:
            if len(self._cache) == self.cap:
                x = self._cache.pop(0)
                del self._cacheMap[x]
            self._cache.append(key)
            self._cacheMap[key] = value

# Your LRUCache object will be instantiated and called as such:
# obj = LRUCache(capacity)
# param_1 = obj.get(key)
# obj.put(key,value)
```


# LeetCode 1. 两数之和
[Two Sum](https://leetcode-cn.com/problems/two-sum/)
## 题目描述
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那**两个**整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

<!-- more -->

**示例:**

> 给定 nums = [2, 7, 11, 15], target = 9

> 因为 nums[0] + nums[1] = 2 + 7 = 9

> 所以返回 [0, 1]

## 方法：哈希表
利用python中的字典记录记录下每个元素出现的位置

```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        
        dic = dict()
        
        for index,value in enumerate(nums):
            sub = target - value
            if sub in dic:
                return [dic[sub], index]
            else:
                dic[value]=index
```

# LeetCode 202. Happy Number
[202. 快乐数](https://leetcode-cn.com/problems/happy-number/)

编写一个算法来判断一个数是不是“快乐数”。

一个“快乐数”定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是无限循环但始终变不到 1。如果可以变为 1，那么这个数就是快乐数。

示例: 
```
输入: 19
输出: true
解释: 
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```
## 方法一
按照“happy number”的定义，直接循环计算各位平方和，观察收敛到1
```python
class Solution:
    def isHappy(self, n: int) -> bool:
        mem = set()
        while n!=1:
            n = sum([int(x)*int(x) for x in list(str(n))])
            if n in mem:
                return False
            else:
                mem.add(n)
        return True
```
## 方法二
先求出100以内的所有happy number， 按照“happy number”的定义，直接循环计算各位平方和，观察收敛到100之内后的数值是否在列表之内。

```python
class Solution:
    def isHappy(self, n: int) -> bool:
        # 100以内的happyNumber
        happySet = set([1, 7, 10, 13, 19, 23, 28, 31, 32, 44, 49, 68, 70, 79, 82, 86, 91, 94, 97])
        while n>99:
            n = sum([int(x)*int(x) for x in list(str(n))])
        return n in happySet
```


# 参考
-    [缓存淘汰算法--LRU算法](https://zhuanlan.zhihu.com/p/34989978)
-    [看动画理解「链表」实现LRU缓存淘汰算法](http://www.cxyxiaowu.com/posts/f5f29cad/)
-    《我的第一本算法书》
-    [Hash Table implementation in Python [Data Structures & Algorithms]](http://blog.chapagain.com.np/hash-table-implementation-in-python-data-structures-algorithms/)


