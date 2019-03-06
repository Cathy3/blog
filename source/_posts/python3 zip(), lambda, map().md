---
title: python3 zip(), lambda, map()
date: 2018-08-19 15:27:33
categories: 
		- python
tags:  
		- python
		- 基础学习
---
# zip() 函数

函数用于将*可迭代的对象*作为参数，将对象中对应的元素打包成一个个*元组*，然后返回由这些元组组成的对象，这样做的好处是节约了不少的内存。简单来说，就是**将序列合并打包**。

我们可以使用`list()`转换来输出列表。

如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表。


<!-- more -->

**语法 :**

    zip(*iterables)
**参数说明：**

iterables -- 一个或多个迭代器,可以内置的迭代器（比如`list`, `string`, `dict`）也可以是用户自定义的迭代器(有`__iter__`方法的对象).


## 例1 基本用法


```python
numberList = [1, 2, 3]
strList = ['one', 'two', 'three']

result = zip() # 没有传入可迭代量

resultList = list(result) # 转为列表
print(resultList)

result = zip(numberList, strList) # 传入两个可迭代量

resultSet = set(result) # 转为集合
print(resultSet)
```

    []
    {(1, 'one'), (3, 'three'), (2, 'two')}



```python
a1, a2 = zip(*zip(a,b))          # 与 zip 相反，*zip 可理解为解压，返回二维矩阵式
list(a1),list(a2)
```




    ([1, 2, 3], [4, 5, 6])



## 例2 传入个数不同的可迭代量


```python
numbersList = [1, 2, 3]
strList = ['one', 'two']
numbersTuple = ('ONE', 'TWO', 'THREE', 'FOUR')

result = zip(numbersList, numbersTuple) # 元素个数与最短的列表一致，3个
resultSet = set(result)
print(resultSet)

result = zip(numbersList, strList, numbersTuple)# 2个元素
resultSet = set(result)
print(resultSet)
```

    {(2, 'TWO'), (1, 'ONE'), (3, 'THREE')}
    {(2, 'two', 'TWO'), (1, 'one', 'ONE')}


## 例3 用*和zip()来解压列表


```python
x = [1,2,3]
y = [4,5,6]
unzipped_x, unzipped_y = zip(*zip(x,y))
print(unzipped_x, unzipped_y)
```

    (1, 2, 3) (4, 5, 6)



```python
zipper_list = [(1, 'a'), (2, 'b'), (3, 'c')]
 
l_a, l_b = zip(*zipper_list)
print(l_a, l_b)
print(list(l_a), list(l_b))
```

    (1, 2, 3) ('a', 'b', 'c')
    [1, 2, 3] ['a', 'b', 'c']


# lambda匿名函数
lambda表达式是一行函数。
它们在其他语言中也被称为**匿名函数**。如果你不想在程序中对一个函数使用两次，你也许会想用lambda表达式，它们和普通的函数完全一样。

**语法**

    lambda 参数:操作(参数)
## 例1 基本用法


```python
add = lambda x, y: x + y
print(add(3, 5))
```

    8


## 例2 列表排序


```python
a = [(1, 2), (4, 1), (9, 10), (13, -3)]
a.sort(key=lambda x: x[1])

print(a)
    # Output: [(13, -3), (4, 1), (1, 2), (9, 10)]
```

    [(13, -3), (4, 1), (1, 2), (9, 10)]


## 例3 在lambda中嵌套逻辑
可以使用if/else三元表达式，或者对等的但需要些技巧的and/or组合。正如我们前面所了解到的，如下语句：
```
if a:
    b
else:
    c
```
能够由以下的概括等效的表达式来模拟：

    b if a else c
    ((a and b) or c)
因为这样类似的表达式能够放在lambda中，所以它们能够在lambda函数中来实现选择逻辑。


```python
lower = (lambda x, y: x if x < y else y)
lower('bb', 'aa')
```




    'aa'



## 例4 在lambda中执行循环
嵌入map调用或列表解析表达式这样的工具来实现。


```python
import sys
showall = lambda x: list(map(sys.stdout.write, x))  # map
showall(['spam\n', 'toast\n', 'eggs\n'])

showall = lambda x: [sys.stdout.write(line) for line in x]  # 列表解析
showall(['music ', 'song ', 'sing'])
```

    spam
    toast
    eggs
    music song sing




    [None, None, None]



# map()函数
**map()** 将一个序列中的每一个迭代元素应用于给定函数， 并返回一个包含所有函数调用结果的一个列表.

**语法 :**

    map(fun, iter)
**参数 :**

`fun` : 给定函数;
`iter` : 可迭代序列(list, tuple 等等) 。

## 例1 基本用法


```python
def addition(n):
    return n + n

numbers = (1, 2, 3, 4)
result = map(addition, numbers)
print(list(result))
```

    [2, 4, 6, 8]


## 例2 在map()中使用lambda函数


```python
numbers = (1, 2, 3, 4)
result = list(map(lambda x: x + x, numbers))
print(result)
```

    [2, 4, 6, 8]


## 例3 传入多个迭代序列


```python
numbers1 = [1, 2, 3]
numbers2 = [4, 5, 6]
 
result = list(map(lambda x, y: x + y, numbers1, numbers2))
print(result)
```

    [5, 7, 9]


## 例4 列表中每个字符串分别序列化


```python
l = ['sat', 'bat', 'cat', 'mat']

test = list(map(list, l))
print(test)
```

    [['s', 'a', 't'], ['b', 'a', 't'], ['c', 'a', 't'], ['m', 'a', 't']]


# 参考
-   [Python zip() - programiz](https://www.programiz.com/python-programming/methods/built-in/zip)
-   [Python3 zip() 函数 | 菜鸟教程](http://www.runoob.com/python3/python3-func-zip.html)
-   [lambda表达式 - python进阶](https://eastlakeside.gitbooks.io/interpy-zh/content/Lambdas/)
-   [Python3之lambda匿名函数详解-Professor哥-51CTO博客](http://blog.51cto.com/professor/1745109)
-   [Python map() function - GeeksforGeeks](https://www.geeksforgeeks.org/python-map-function/)
