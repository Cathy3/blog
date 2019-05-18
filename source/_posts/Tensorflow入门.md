---
title: Tensorflow入门
date: 2019-05-09
categories: 
	    -   机器学习
tags:  
        -   深度学习
        -   tensorflow
        -   神经网络
        -   基础
mathjax: true
---

这里编写一个最简单的神经网络，以此了解深度学习的整个训练过程。

<!-- more -->

# Imports
导入keras框架，用来定义神经网络的一系列Sequential层
```python
import tensorflow as tf
import numpy as np
from tensorflow import keras
```

# Define and Compile the Neural Network

创建最简单的神经网络，1 layer, 1 neuron, input shape只有1个value
```python
model = tf.keras.Sequential([keras.layers.Dense(units=1, input_shape=[1])])
```

compile 神经网络，指定了两个函数，
-   loss： 计算猜测的值与已知的真实值之间的差距，判断猜测的好不好。
-   optimizer：根据loss结果，重新猜测多次，以最小化loss
```python
model.compile(optimizer='sgd', loss='mean_squared_error')
```

# Providing the Data
输入一些数据，numpy把数据变成array类型。 能够看出来他们的关系是 Y=2X-1,
```python
xs = np.array([-1.0, 0.0, 1.0, 2.0, 3.0, 4.0], dtype=float)
ys = np.array([-3.0, -1.0, 1.0, 3.0, 5.0, 7.0], dtype=float)
```

# Training the Neural Network
训练过程中，神经网络学习 xs 与 ys 之间的关系。根据 loss , 用 optimizer 重复猜测 epochs 次，
```python
model.fit(xs, ys, epochs=500)
```

预测一个数。由于神经网络是做概率计算，所以虽然能找到这个数据关系 Y=2X-1,但是预测结果只能非常接近19，不可能直接是19，而且训练数据量只有6个，太少。
```python
print(model.predict([10.0]))
```

# 参考 
[课程 Introduction to TensorFlow for Artificial Intelligence, Machine Learning, and Deep Learning](https://www.coursera.org/learn/introduction-tensorflow)