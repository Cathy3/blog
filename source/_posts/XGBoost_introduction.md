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
-    对缺失值处理
-    优缺点
-    sklearn参数

<!-- more -->
目前来说，gradient boosting是对表格数据进行预测的最有力的方法。而XGBoost方法(eXtreme Gradient Boosting)是gradient boosting的最快实现。

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

注：$w_q(x)$ 为叶子节点 $q$ 的分数，$f(x)$为其中一棵回归树

# XGBoost原理
XGBoost目标函数定义为：

$$
O b j=\sum_{i=1}^{n} l\left(y_{i}, \hat{y}_{i}\right)+\sum_{k=1}^{K} \Omega\left(f_{k}\right)
\\
where\ \Omega(f)=\gamma T+\frac{1}{2} \lambda\|w\|^{2}
$$

目标函数由两部分构成，第一部分用来衡量预测分数和真实分数的差距（损失），另一部分则是正则化项。

正则化项同样包含两部分，$T$ 表示叶子结点的个数，$w$ 表示叶子结点的分数。$γ$ 可以控制叶子结点的个数，$λ$ 可以控制叶子节点的分数不会过大，防止过拟合。$y_i$ 即为真实值，$\hat{y}_{i}$ 为预测值。

正如上文所说，新生成的树是要拟合上次预测的残差的，即当生成 $t$ 棵树后，预测分数可以写成：

$$
\hat{y}_{i}^{(t)}=\hat{y}_{i}^{(t-1)}+f_{t}\left(x_{i}\right)
$$

同时，可以将目标函数改写成：

$$
\mathcal{L}^{(t)}=\sum_{i=1}^{n} l\left(y_{i}, \hat{y}_{i}^{(t-1)}+f_{t}\left(x_{i}\right)\right)+\Omega\left(f_{t}\right)
$$

很明显，我们接下来就是要去找到一个 $f_t$ 能够最小化目标函数。XGBoost的想法是利用其在 $f_t=0$ 处的泰勒二阶展开近似它。

泰勒展开：$f(x+\Delta x) \simeq f(x)+f^{\prime}(x) \Delta x+\frac{1}{2} f^{\prime \prime}(x) \Delta x^{2}$

先定义一阶导数和二阶导数：$g_{i}=\partial_{\hat{y}^{(t-1)}} l\left(y_{i}, \hat{y}^{(t-1)}\right), \quad h_{i}=\partial_{\hat{y}^{(t-1)}}^{2} l\left(y_{i}, \hat{y}^{(t-1)}\right)$

所以，目标函数近似为：

$$
O b j^{(t)} \simeq \sum_{i=1}^{n}\left[l\left(y_{i}, \hat{y}_{i}^{(t-1)}\right)+g_{i} f_{t}\left(x_{i}\right)+\frac{1}{2} h_{i} f_{t}^{2}\left(x_{i}\right)\right]+\Omega\left(f_{t}\right)+ constant
$$

由于前t-1棵树的预测分数与y的残差对目标函数优化不影响，可以直接去掉。我们知道，每个样本都最终会落到一个叶子结点中，所以我们可以将所以同一个叶子结点的样本重组起来，过程如下：

$$
\begin{aligned} O b j^{(t)} & \simeq \sum_{i=1}^{n}\left[g_{i} f_{t}\left(x_{i}\right)+\frac{1}{2} h_{i} f_{t}^{2}\left(x_{i}\right)\right]+\Omega\left(f_{t}\right) \\ &=\sum_{i=1}^{n}\left[g_{i} w_{q\left(x_{i}\right)}+\frac{1}{2} h_{i} w_{q\left(x_{i}\right)}^{2}\right]+\gamma T+\lambda \frac{1}{2} \sum_{j=1}^{T} w_{j}^{2} \\ &=\sum_{j=1}^{T}\left[\left(\sum_{i \in I_{j}} g_{i}\right) w_{j}+\frac{1}{2}\left(\sum_{i \in I_{j}} h_{i}+\lambda\right) w_{j}^{2}\right]+\gamma T \end{aligned}
$$

因此通过上式的改写，我们可以将目标函数改写成关于叶子结点分数 $w$ 的一个一元二次函数，求解最优的 $w$ 和目标函数值就变得很简单了，直接使用顶点公式即可。因此，最优的 $w$ 和目标函数公式为

$$
w_{j}^{*}=-\frac{G_{j}}{H_{j}+\lambda}
\\
O b j=-\frac{1}{2} \sum_{j=1}^{T} \frac{G_{j}^{2}}{H_{j}+\lambda}+\gamma T
$$

# 分裂结点算法
在上面的推导中，我们知道了如果我们一棵树的结构确定了，如何求得每个叶子结点的分数。但我们还没介绍如何确定树结构，即每次特征分裂怎么寻找最佳特征，怎么寻找最佳分裂点。

正如上文说到，基于空间切分去构造一颗决策树是一个NP难问题，我们不可能去遍历所有树结构，因此，XGBoost使用了和CART回归树一样的想法，利用贪婪算法，遍历所有特征的所有特征划分点，不同的是使用上式目标函数值作为评价函数。具体做法就是对比分裂后的目标函数值与单子叶子节点的目标函数增益，同时为了限制树生长过深，还加了个阈值，只有当增益大于该阈值才进行分裂。

同时可以设置树的最大深度、当样本权重和小于设定阈值时停止生长去防止过拟合。

# Shrinkage and Column Subsampling
XGBoost还提出了两种防止过拟合的方法：Shrinkage and Column Subsampling。

Shrinkage方法就是在每次迭代中对树的每个叶子结点的分数乘上一个缩减权重η，这可以使得每一棵树的影响力不会太大，留下更大的空间给后面生成的树去优化模型。

Column Subsampling类似于随机森林中的选取部分特征进行建树。其可分为两种，
-    一种是按层随机采样，在对同一层内每个结点分裂之前，先随机选择一部分特征，然后只需要遍历这部分的特征，来确定最优的分割点。
-    另一种是随机选择特征，建树前随机选择一部分特征然后分裂就只遍历这些特征。一般情况下前者效果更好。

# 近似算法
对于连续型特征值，当样本数量非常大，该特征取值过多时，遍历所有取值会花费很多时间，且容易过拟合。因此XGBoost思想是对特征进行分桶，即找到l个划分点，将位于相邻分位点之间的样本分在一个桶中。在遍历该特征的时候，只需要遍历各个分位点，从而计算最优划分。

从算法伪代码中该流程还可以分为两种，全局的近似是在新生成一棵树之前就对各个特征计算分位点并划分样本，之后在每次分裂过程中都采用近似划分，而局部近似就是在具体的某一次分裂节点的过程中采用近似算法。


# 针对稀疏矩阵的算法（缺失值处理）
当样本的第 $i$ 个特征值缺失时，无法利用该特征进行划分时，XGBoost的想法是将该样本分别划分到左结点和右结点，然后计算其增益，哪个大就划分到哪边。


# XGBoost的优点
1.   使用许多策略去防止过拟合，如：正则化项、Shrinkage and Column Subsampling等。
2.   目标函数优化利用了损失函数关于待求函数的二阶导数
3.   支持并行化，这是XGBoost的闪光点，虽然树与树之间是串行关系，但是同层级节点可并行。具体的对于某个节点，节点内选择最佳分裂点，候选分裂点计算增益用多线程并行。训练速度快。
4.    添加了对稀疏数据的处理。
5.    交叉验证，early stop，当预测结果已经很好的时候可以提前停止建树，加快训练速度。
6.    支持设置样本权重，该权重体现在一阶导数g和二阶导数h，通过调整权重可以去更加关注一些样本。

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
-    [XGBoost-ZDK](https://zdkswd.github.io/2019/02/25/XGBoost/) 
-    [一文读懂机器学习大杀器XGBoost原理](https://mp.weixin.qq.com/s/AnENu0i3i5CdUQkZscMKgQ)
-    [XGBoost-Python完全调参指南-参数解释篇](https://blog.csdn.net/wzmsltw/article/details/50994481)
-    [Complete Guide to Parameter Tuning in XGBoost (with codes in Python)](https://www.analyticsvidhya.com/blog/2016/03/complete-guide-parameter-tuning-xgboost-with-codes-python/)
-    [XGBoost：参数解释](http://blog.csdn.net/zc02051126/article/details/46711047)
-    [xgboost入门与实战（原理篇）](https://blog.csdn.net/sb19931201/article/details/52557382)
-    [XGBoost](https://zdkswd.github.io/2019/02/25/XGBoost/)