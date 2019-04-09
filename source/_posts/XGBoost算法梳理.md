---
title: XGBoost算法梳理
date: 2019-04-09
categories: 
	-   机器学习
tags:  
        -   sklearn
        -   机器学习
        -   算法
mathjax: true
---

**学习任务：**

-    算法原理
-    损失函数
-    分裂结点算法
-    正则化
-    对缺失值处理
-    优缺点
-    应用场景
-    sklearn参数

<!-- more -->

2014年，陈天奇博士提出了XGBoost算法，它可认为是在GBDT算法基础上的进一步优化。首先，XGBoost算法在基学习器损失函数中引入了正则项，控制减少训练过程当中的过拟合；其次，XGBoost算法不仅使用一阶导数计算伪残差，还计算二阶导数可近似快速剪枝的构建新的基学习器；此外，XGBoost算法还做了很多工程上的优化，例如支持并行计算、提高计算效率、处理稀疏训练数据等等。

总的来说，XGBoost算法源起于Boosting集成学习方法，在演化过程中又融入了Bagging集成学习方法的优势，通过Gradient Boosting框架自定义损失函数提高了算法解决通用问题的能力，同时引入更多可控参数即可针对问题场景进行优化，最后通过工程实现方面细节优化，在保证算法结果稳定的同时还可高效处理大规模数据，可扩展支持不同编程语言。这些因素共同使它成为了工业界的主流机器学习算法之一。

XGBoost是一种提升树模型，所以它是将许多树模型集成在一起，形成一个很强的分类器。而所用到的树模型则是CART回归树模型。讲解其原理前，先讲解一下CART回归树。

# CART回归树
CART回归树是假设树为二叉树，通过不断将特征进行分裂。

比如当前树结点是基于第 $j$ 个特征值进行分裂的，设该特征值小于 $s$ 的样本划分为左子树，大于s的样本划分为右子树。

$$
R_{1}(j, s)=\left\{x | x^{(j)} \leq s\right\} \text { and } R_{2}(j, s)=\left\{x | x^{(j)}>s\right\}
$$

而CART回归树实质上就是在该特征维度对样本空间进行划分，而这种空间划分的优化是一种NP难问题，因此，在决策树模型中是使用启发式方法解决。典型CART回归树产生的目标函数为：

$$
\sum_{x_{i} \in R_{m}}\left(y_{i}-f\left(x_{i}\right)\right)^{2}
$$

因此，当我们为了求解最优的切分特征j和最优的切分点s，就转化为求解这么一个目标函数：

$$
\min _{j, s}\left[\min _{c_{1}} \sum_{x_{i} \in R_{1}(j, s)}\left(y_{i}-c_{1}\right)^{2}+\min _{c_{2}} \sum_{x_{i} \in R_{2}(j, s)}\left(y_{i}-c_{2}\right)^{2}\right]
$$

所以我们只要遍历所有特征的的所有切分点，就能找到最优的切分特征和切分点。最终得到一棵回归树。
# XGBoost算法思想
该算法思想就是不断地添加树，不断地进行特征分裂来生长一棵树，每次添加一个树，其实是学习一个新函数，去拟合上次预测的残差。当我们训练完成得到k棵树，我们要预测一个样本的分数，其实就是根据这个样本的特征，在每棵树中会落到对应的一个叶子节点，每个叶子节点就对应一个分数，最后只需要将每棵树对应的分数加起来就是该样本的预测值。

$$
\hat{y}=\phi\left(x_{i}\right)=\sum_{k=1}^{K} f_{k}\left(x_{i}\right)
$$

$$
where\ F=\left\{f(x)=w_{q(x)}\right\}\left(q : R^{m} \rightarrow T, w \in R^{T}\right)
$$

注：w_q(x)为叶子节点q的分数，f(x)为其中一棵回归树


# xgboost参数
官方参数介绍看这里： 
Parameters (official guide)

## General Parameters（常规参数） 
1.   booster [default=gbtree]：选择基分类器，gbtree: tree-based models/gblinear: linear models 
2.   silent [default=0]:设置成1则没有运行信息输出，最好是设置为0. 
3.   nthread [default to maximum number of threads available if not set]：线程数

## Booster Parameters（模型参数） 
1.eta [default=0.3]:shrinkage参数，用于更新叶子节点权重时，乘以该系数，避免步长过大。参数值越大，越可能无法收敛。把学习率 eta 设置的小一些，小学习率可以使得后面的学习更加仔细。 

2.min_child_weight [default=1]:这个参数默认是 1，是每个叶子里面 h 的和至少是多少，对正负样本不均衡时的 0-1 分类而言，假设 h 在 0.01 附近，min_child_weight 为 1 意味着叶子节点中最少需要包含 100 个样本。这个参数非常影响结果，控制叶子节点中二阶导的和的最小值，该参数值越小，越容易 overfitting。 

3.max_depth [default=6]: 每颗树的最大深度，树高越深，越容易过拟合。 

4.max_leaf_nodes:最大叶结点数，与max_depth作用有点重合。 

5.gamma [default=0]：后剪枝时，用于控制是否后剪枝的参数。 

6.max_delta_step [default=0]：这个参数在更新步骤中起作用，如果取0表示没有约束，如果取正值则使得更新步骤更加保守。可以防止做太大的更新步子，使更新更加平缓。 

7.subsample [default=1]：样本随机采样，较低的值使得算法更加保守，防止过拟合，但是太小的值也会造成欠拟合。 

8.colsample_bytree [default=1]：列采样，对每棵树的生成用的特征进行列采样.一般设置为： 0.5-1 

9.lambda [default=1]：控制模型复杂度的权重值的L2正则化项参数，参数越大，模型越不容易过拟合。

10.alpha [default=0]:控制模型复杂程度的权重值的 L1 正则项参数，参数值越大，模型越不容易过拟合。

11.scale_pos_weight [default=1]：如果取值大于0的话，在类别样本不平衡的情况下有助于快速收敛。

## Learning Task Parameters（学习任务参数） 
1.objective [default=reg:linear]：定义最小化损失函数类型，常用参数： 
binary:logistic –logistic regression for binary classification, returns predicted probability (not class) 
multi:softmax –multiclass classification using the softmax objective, returns predicted class (not probabilities) 
you also need to set an additional num_class (number of classes) parameter defining the number of unique classes 
multi:softprob –same as softmax, but returns predicted probability of each data point belonging to each class. 

2.eval_metric [ default according to objective ]： 
The metric to be used for validation data. 
The default values are rmse for regression and error for classification. 
Typical values are: 
rmse – root mean square error 
mae – mean absolute error 
logloss – negative log-likelihood 
error – Binary classification error rate (0.5 threshold) 
merror – Multiclass classification error rate 
mlogloss – Multiclass logloss 
auc: Area under the curve

3.seed [default=0]： 
The random number seed. 随机种子，用于产生可复现的结果 
Can be used for generating reproducible results and also for parameter tuning.

注意: python sklearn style参数名会有所变化 
eta –> learning_rate 
lambda –> reg_lambda 
alpha –> reg_alpha


# 参考  
-    [XGBoost-Python完全调参指南-参数解释篇](https://blog.csdn.net/wzmsltw/article/details/50994481)
-    [Complete Guide to Parameter Tuning in XGBoost (with codes in Python)](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/)
-    [XGBoost：参数解释](http://blog.csdn.net/zc02051126/article/details/46711047)
-    [xgboost入门与实战（原理篇）](https://blog.csdn.net/sb19931201/article/details/52557382)
-    [XGBoost](https://zdkswd.github.io/2019/02/25/XGBoost/)