---
title: python实现排序算法
date: 2019-03-04
categories: 
	-   Algorithm
tags:  
        -   python
        -   LeetCode
        -   数据结构
        -   排序
mathjax: true
---
学习目标：

-   实现归并排序、快速排序、插入排序、冒泡排序、选择排序、堆排序
-   LeetCode 215. 数组中的第K个最大元素

<!-- more -->

# 插入排序
每次假设前面的元素都是已经排好序了的，然后将当前位置的元素插入到前面已排好序的序列中。
```python
def insert_sort(lst):
    for index in range(1, len(lst)): #开始时[0,1]已排序
        x = lst[index] #待排序的元素
        p = index # 从位置j往前开始比较
        while p>0 and lst[p-1] > x:
            lst[p] = lst[p-1]
            p -= 1
        lst[p] = x

lst = [63,21,44,3,67,9,6,86,12]
insert_sort(lst)
print(lst)
```
空间复杂度是$O(1)$

平均时间复杂度$O(n^2)$

## 二分查找的插入排序
在插人排序中需要检索元素的插人位置， 而且是在排序的(部分)序列里检索。 这提示了另一可能方案： 采用二分查找。
```python
def insert_sort_binarysearch(lst):
    for index in range(1, len(lst)):
        x = lst[index]
        p = index
        low = 0
        high = index-1
        while high >= low:
            mid = (high+low)//2
            if lst[mid] > x:
                high = mid-1
            else:
                low = mid+1
        while p > low:
            lst[p] = lst[p-1]
            p -= 1
        lst[p] = x

lst = [63,21,44,3,67,9,6,86,12]
insert_sort_binarysearch(lst)
print(lst)
```
但这种做法不可能从根本上改变算法的性质： 虽然每次检索位置的代价降低了， 但找到位置后还需要顺序移动元素， 腾出空位将元素插人。 后一操作仍然可能需要线性时间。



# 选择排序
思路：
1.   以空序列作为排序工作的开始，
2.   遍历，每次从剩余未排序的元素中选取最小值， 将其放在已排序的$i$个元素的后面，作为序列的第$i+ 1$个元素， 使已排序序列增长。
3.   做到尚未排序的序列里只剩一个元素时（它必然为最大)，只需直接将其放在已排序的记录之后， 整个排序就完成了。

需要解决两个问题： 第一是如何选择元素； 第二是做出适当安排，尽可能利用现有序列的存储空间，避免另行安排存储。
-    最简单的选择方法是顺序扫描序列中的元素， 记住遇到的最小元素。 一次扫描完毕就找到了一个最小元素。反复扫描就能完成排序工作。 
-    选出了一个元素，原来的序列中就出现了一个空位，可以把这些空位集中起来存放排好序的序列。

在排序过程中的任何时刻， 表的前段积累了一批递增的已经排好序的元素，而且它们都不大于任何一个未排序记录。 下一步从未排序段中选岀最小的元素， 将其存放在已排序元素段的后面(直接交换紧随已排序段的那个位置和最小值)。 这样在只剩一个元素时， 一定最大值， 工作即可结束。

```python
def select_sort(lst):
    for index in range(len(lst)-1): #不需要循环最后一个元素
        k = index # 从index位置开始往后遍历比较找到最小元素
        for j in range(index, len(lst)):
            if lst[j] < lst[k]:
                k = j
        if index != k: # 确认不是同一个位置
            lst[index],lst[k] = lst[k], lst[index] #交换元素    
    
lst = [63,21,44,3,67,9,6,86,12]
select_sort(lst)
print(lst)
```
空间复杂度是$O(1)$

时间复杂度是$O(n^2)$

选择排序比较低效， 原因就在于其中的顺序比较： 每次选择一个元素， 都是从头开始做一遍完全的比较， 在整个排序过程中做了很多重复比较工作。


# 堆排序
如果在一个连续表里存储的数据是一个小顶堆（ 元素之间的关系满足堆序 )， 按优先队列的操作方式反复弹出堆顶元素， 能够得到一个递增序列。 

```python
def heap_sort(elems):
    def siftdown(elems,e,begin,end):# 建立大根堆，排序
        i, j = begin, begin*2+1
        while j < end:
            if j+1<end and elems[j+1] > elems[j]:
                j = j+1 #左右子树比较，选择较大的为j
            if e > elems[j]:
                break #e比左右子树都大，那就不用下移比较了
            elems[i] = elems[j]
            i, j = j, j*2+1 #下移
        elems[i] = e
    
    end = len(elems)
    for i in range(end//2,-1,-1):#建立堆
        siftdown(elems,elems[i],i,end)
    for i in range(end-1, 0, -1):#排序
        e = elems[i]
        elems[i] = elems[0]
        siftdown(elems,e,0,i)
 
l=[-1,26,5,77,1,61,11,59,15,48,19] 
heap_sort(l)
print(l)
```
时间复杂度是$O(nlogn)$

# 冒泡排序
冒泡排序就是**重复**“从序列右边开始比较相邻两个数字的大小，再根据结果交换两个数字的位置”**这一操作**的算法。

每一轮操作（比较和交换）都会有一个最大值移到右端。

在这个过程中，数字会像泡泡一样，慢慢从左往右“浮”到序列的一端，所以这个算法才被称为“冒泡排序”。

通过一遍遍扫描，表的最后将积累起越来越多排好顺序的大元素。 每遍扫描，这段元素增加一个，经过
n-1 遍扫描，一定能完成排序。 此外，做一遍，扫描的范围可以缩短一项。 把这些考虑综合起来就得到了下面的算法：
```python
def bubble_sort(lst):
    for i in range(len(lst)):
        for j in range(1,len(lst)-i):
            if lst[j-1] > lst[j]:
                lst[j-1],lst[j] = lst[j], lst[j-1]
                
 
l=[-1,26,5,77,1,61,11,59,15,48,19] 
bubble_sort(l)
print(l)
```
## 改进
虽然有时起泡排序确实需要做满 n-1 遍，但那是特例，只有被排序表的最小元素恰好在最后时才会出现这种情况。 

在其他情况下，扫描就不需要做那么多次，如果发现排序已经完成就可以及早结束。 

如果在一次扫描中没遇到逆序，就说明排序工作已经完成， 可以提前结束了。
```python
def bubble_sort(lst):
    for i in range(len(lst)):
        found = False
        for j in range(1,len(lst)-i):
            if lst[j-1] > lst[j]:
                lst[j-1],lst[j] = lst[j], lst[j-1]
                found = True #有逆序
        if not found:# 如果没有逆序
            break #可以跳出循环结束了
 
l=[-1,26,5,77,1,61,11,59,15,48,19] 
bubble_sort(l)
print(l)
```


# 归并排序
归并排序算法会把序列分成长度相同的两个子序列，当无法继续往下分时（也就是每个子序列中只有一个数据时），就对子序列进行归并。归并指的是把两个排好序的子序列合并成一个有序序列。该操作会一直重复执行，直到所有子序列都归并为一个整体为止。

归并排序中，分割序列所花费的时间不算在运行时间内（可以当作序列本来就是分割好的）。在合并两个已排好序的子序列时，只需重复比较首位数据的大小，然后移动较小的数据，因此只需花费和两个子序列的长度相应的运行时间。

也就是说，完成一行归并所需的运行时间取决于这一行的数据量。 无论哪一行都是n个数据，所以每行的运行时间都为 O(n)。 而将长度为 n 的序列对半分割直到只有一个数据为止时，可以分成$log_2n$行，因此，总 共有$log_2n$行。也就是说，总的运行时间为 O(nlogn)

```python
def mergesort(lst):
    if len(lst) <= 1:
        return lst
    mid = len(lst)//2 # 将列表分成更小的两个列表
    # 分别对左右两个列表进行处理，分别返回两个排序好的列表
    left = mergesort(lst[:mid])
    right = mergesort(lst[mid:])
    # 对排序好的两个列表合并，产生一个新的排序好的列表
    return merge(left, right)

def merge(left, right):
    res = []
    i = 0
    j = 0
    # 对两个列表中的元素 两两对比。
    # 将最小的元素，放到res中
    while i<len(left) and j<len(right):
        if left[i] <= right[j]:
            res.append(left[i])
            i += 1
        else:
            res.append(right[j])
            j += 1
    res += left[i:]
    res += right[j:]
    return res
 
l=[-1,26,5,77,1,61,11,59,15,48,19] 
res = mergesort(l)
print(res)
```



# 快速排序
快速排序算法首先会在序列中随机选择一个基准值（pivot），然后将除了基准值以外的数分为“比基准值小的数”和“比基准值大的数”这两个类别，再将其排列成以下形式。

`[ 比基准值小的数 ] 基准值 [ 比基准值大的数 ]`

接着，对两个“[ ]”中的数据进行排序之后，整体的排序便完成了。对“[ ]”里面的数据进行排序时同样也会使用快速排序(递归)。

```python
def qsort_rec(lst, l, r):
    if l>=r:
        return
    i = l
    j = r
    pivot = lst[i]
    while i<j: # 找pivot的最终位置
        while i<j and lst[j]>=pivot:
            j -= 1 # 用j向左扫描找小于pivot的记录
        if i<j:
            lst[i] = lst[j]
            i += 1 # 小记录移到左边
        while i<j and lst[i]<pivot:
            i += 1
        if i<j:
            lst[j] = lst[i]
            j -= 1
    lst[i] = pivot
    qsort_rec(lst, l, i-1) # 递归处理左半区间
    qsort_rec(lst, i+1, r) # 递归处理左右半区间
 
l=[-1,26,5,77,1,61,11,59,15,48,19] 
qsort_rec(l,0,len(l)-1)
print(l)
```

时间复杂度$O(nlogn)$

## 改进
```python
def quick_sort1(lst):
    def qsort(lst, begin,end):
        if begin >= end:
            return
        pivot = lst[begin]
        i = begin
        for j in range(begin+1, end+1):
            if lst[j] < pivot:
                i += 1
                lst[i],lst[j] = lst[j],lst[i]
        lst[begin],lst[i] = lst[i], lst[begin]
        qsort(lst, begin, i-1)
        qsort(lst,i+1, end)
    qsort(lst,0,len(lst)-1)
l=[-1,26,5,77,1,61,11,59,15,48,19] 
quick_sort1(l)
print(l)
```

# LeetCode 215. 数组中的第K个最大元素
在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

示例 1:
```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```
示例 2:
```
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```
说明: 你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

## 方法：快速排序中选择
```python
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:    
        def qsort(lst, begin,end):
            if begin >= end:
                return
            pivot = lst[begin]
            i = begin
            for j in range(begin+1, end+1):
                if lst[j] > pivot:
                    i += 1
                    lst[i],lst[j] = lst[j],lst[i]
            lst[begin],lst[i] = lst[i], lst[begin]
            if i>k-1:
                qsort(lst, begin, i-1)
            elif i<k-1:
                qsort(lst,i+1, end)
            else:
                return
        qsort(nums,0,len(nums)-1)
        return nums[k-1]
```

## 方法改进
```python
import random
class Solution:
    def findKthLargest(self, nums, k):
        pivot = random.choice(nums)
        nums1, nums2 = [], []
        for num in nums:
            if num > pivot:
                nums1.append(num)
            elif num < pivot:
                nums2.append(num)
        if k <= len(nums1):
            return self.findKthLargest(nums1, k)
        if k > len(nums) - len(nums2):
            return self.findKthLargest(nums2, k - (len(nums) - len(nums2)))
        return pivot
```

# 参考
-   《数据结构(C++语言版)》
-   [Python Data Structures - C2 Sort](https://hujiaweibujidao.github.io/blog/2014/05/07/python-data-structures---c2-sort/)
-   [python归并排序--递归实现](https://www.jianshu.com/p/3ad5373465fd)
