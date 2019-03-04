---
title: Maximum Pairwise Product Problem
date: 2018-09-25
categories: 
		- Algorithm
tags:  
		- python
        - Algorithm		
mathjax: true
---


# 问题：两数最大乘积
Find the maximum product of two distinct numbers in a sequence of non-negative integers.

**Input:** A sequence of non-negative integers.

**Output:** The maximum value that can be obtained by multiplying two different elements from the sequence.

**Constraints:** $ 2 ≤ n ≤ 2 · 10^5; 0 ≤ a_1,...,a_n ≤ 2 · 10^5 $.

Sample:
```
Input:
1 2 3
Output:
6
```

<!-- more -->

这个问题其实就是要找到列表中最大的两个数。
# Naive Algorithm


```python
def max_pairwise_productNaive(A):
    n = len(A)
    max_product = 0
    for i in range(n):
        for j in range(n):
            if i != j:
                if max_product < A[i] * A[j]:
                    max_product = A[i] * A[j]
    return max_product

max_pairwise_productNaive([9,2,4,5,6,7])
```




    63



代码可以优化为如下形式：


```python
def max_pairwise_productNaive(A):
    n = len(A)
    max_product = 0
    for i in range(n):
        for j in range(i+1, n):
            max_product = max(max_product, A[i]*A[j])
    return max_product

max_pairwise_productNaive([9,2,4,5,6,7])
```




    63



这样的代码在数值很大时会超过运行时间限制。

# Fast Algorithm
第一次循环找到最大值，第二次循环找到除此之外的最大值。经过 2n 次比较,运行时间 O(n)


```python
def max_pairwise_productFast(A):
    n = len(A)
    j = 0
    for i in range(1, n):
        if A[i] > A[j]:
            j = i
    
    if j == 0:
        h = 1
    else:
        h = 0
    
    for i in range(1, n):
        if i!=j and A[i] > A[h]:
            h = i
    return A[j]*A[h]

max_pairwise_productFast([9,2,4,5,6,7])
```




    63



## 压力测试
是一种test和debug的方法。


```python
import random
 
def StressTest(N, M):
    while True:
        n = random.randint(2, N)
        A=[random.randint(0,M) for i in range(n)]
        print(A)
        result_1 = max_pairwise_productNaive(A)
        result_2 = max_pairwise_productFast(A)
        
        if result_1 == result_2:
            print("OK")
        else:
            print("Wrong answer:",result_1,result_2)
    return
StressTest(10,100000)
```

    

# 找出列表中最大的两个数


```python
def max_pairwise(A):
    n = len(A)
    
    max1 = A[0] 
    max2 = A[1]
    if A[0] < A[1]:
        max1, max2 = max2, max1
        
    for i in range(2,n):
        if A[i] >= max1:
            max2 = max1
            max1 = A[i]
        elif(A[i] < max1 and A[i] > max2):
            max2 = A[i]
    return max1, max2

max_pairwise([20,9,2,4,5,11,6,7,4,9,10])
```




    (20, 11)



# 更简洁的写法
但运行时间较长，为 O(nlogn)


```python
def max_pairwise_productSort(A):
    n = len(A)
    A.sort()
    return A[n-1] * A[n-2]

max_pairwise_productSort([9,2,4,5,6,7])
```




    63


# 参考
 - [Coursera_algorithmic-toolbox_week1](https://www.coursera.org/learn/algorithmic-toolbox/home/week/1)
