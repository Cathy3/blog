---
title: pandas 处理时间类型数据
date: 2019-04-01
categories: 
	-  数据分析
tags:  
        -   python
        -   pandas
        -   时间序列
        -   数据分析
mathjax: true
---
**学习目标：**
-   时间序列数据导入，并转为时间类型
-   



导入一个时间相关的数据集
```python
import numpy as np
import pandas as pd

## importing a dataset
url="http://mlr.cs.umass.edu/ml/machine-learning-databases/event-detection/CalIt2.data"
data = pd.read_csv(url, header = None)
```

查看数据的属性

```python
>>> data.head(3)
   0         1         2  3
0  7  07/24/05  00:00:00  0
1  9  07/24/05  00:00:00  0
2  7  07/24/05  00:30:00  1

## 查看所有列的数据类型
>>> data.dtypes
0     int64
1    object
2    object
3     int64
dtype: object
```
可以看出来第1列和第2列包含时间时间序列数据，但却是object类型，我们可以转换成时间类型。
## 数据类型转化(object → datetime)
```python
>>> data[1] = pd.to_datetime(data[1])

>>> data[2] = pd.to_datetime(data[2])

>>> data.dtypes
0             int64
1    datetime64[ns]
2    datetime64[ns]
3             int64
dtype: object
```
## 查看datetime类型数据
- 查看这个数据集中的时间数据中包含了哪些年、月、日等。

```python
>>> data[1].dt.year.unique()
array([2005], dtype=int64)

>>> data[1].dt.month.unique()
array([ 7,  8,  9, 10, 11], dtype=int64)

>>> data[1].dt.day.unique()
array([24, 25, 26, 27, 28, 29, 30, 31,  1,  2,  3,  4,  5,  6,  7,  8,  9,
       10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23], dtype=int64)
```
- 查看某月的数据有多少
```python
>>> data[data[1].dt.month == 10].shape
(2976, 4)
```
- 查看两个时间相差多久

```python
>>> data.loc[6624,1] - data.loc[0,1]
Timedelta('69 days 00:00:00')
```
第1行和第6624行的数据相差69天，结果是个Timedelta数据类型。


参考：[Working with Dates and timedelta in Pandas]( https://stepik.org/lesson/44678/step/1?unit=22718)