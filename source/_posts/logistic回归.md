---
title: Logistic回归
date: 2018-01-23 15:27:33
categories: 
		- 机器学习
tags:  
		- 机器学习
		- 原理
		- Logistic回归
---
# 基本原理
Logistic回归是统计学习中的经典分类方法。其实仅在线性回归的基础上，套用了一个逻辑函数。本文主要详述逻辑回归模型的基础。

假设有输入向量 $x=(x_1;x_2;...;x_n)$， 由线性回归模型产生的预测值 

$$z=w ^ \mathrm{ T }x+b\tag{1}$$ 是实值，需要转换为 0/1 值才能进行分类。因此找一个单调可微函数来替代，将$z$ 的值映射到 (0,1) 之间，这里采用[logistic函数](https://en.wikipedia.org/wiki/Logistic_function)：
$$y=\frac{1}{1+e^{-z}}\tag{2}$$
![logistic function](https://upload.wikimedia.org/wikipedia/commons/thumb/8/88/Logistic-curve.svg/1200px-Logistic-curve.svg.png)
从图中可以看出，对数几率函数是一种“Sigmoid函数”，当 $z=0$ 时，函数值为0.5。随着 $z$ 的增大，对应的值将逼近于1；而随着 $z$ 的减小，输出值将逼近于0。如果横坐标刻度足够大，函数将看起来很像一个阶跃函数，其输出值在 $z=0$ 附近变化很陡。

将式(1)代入得到             

$$y=\frac{1}{1+e^{-(w ^ \mathrm{ T }x+b)}}\tag{3}$$ 

**上式就是logistic回归模型的分类函数。**最后， $y$ 是一个范围在0~1之间的数值。结果大于0.5的数据被归入1类，小于0.5的即被归入0类。所以，Logistic回归也可以被看成是一种概率估计。

(3)式可变化为对数几率形式：

$$\ln \frac{y}{1-y}=w ^ \mathrm{ T }x+b\tag{4}$$
由此看出，**真实标记 $y$ 的对数几率是输入 $x$ 的线性函数**。
将 $y$ 视为类后验概率估计 $p(y=1|x)$ ，则上式可重写为

$$\ln \frac{p(y=1|x)}{p(y=0|x)}=w ^ \mathrm{ T }x+b\tag{5}$$

显然有

$$p(y=1|x)=\frac{e^{w ^ \mathrm{ T }x+b}}{1+e^{w ^ \mathrm{ T }x+b}}\tag{6}$$
$$p(y=0|x)=\frac{1}{1+e^{w ^ \mathrm{ T }x+b}}\tag{7}$$
**此时线性函数的值越接近正无穷，概率值就越接近1；线性函数的值越接近负无穷，概率值就越接近0。由此得以分类。**

接下来可通过“极大似然法”来估计 $w$ 和 $b$ 。
# 用优化方法来估计参数
优化方法有很多，这里主要了解梯度下降，牛顿法和BFGS

# 参考
- [浅析Logistic Regression](https://chenrudan.github.io/blog/2016/01/09/logisticregression.html)