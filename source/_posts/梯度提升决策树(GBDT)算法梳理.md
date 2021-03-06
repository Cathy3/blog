---
title: 梯度提升树(GBDT)算法梳理
date: 2019-04-04
categories: 
	-   机器学习
tags:  
        -   sklearn
        -   机器学习
        -   算法
mathjax: true
---
梯度提升决策树（Gradient Boosting Decision Tree， GBDT）是Boosting算法中非常流行的模型，也是近来在机器学习竞赛、商业应用中表现都非常优秀的模型。GBDT非常好地体现了“从错误中学习”的理念，基于决策树预测的残差进行迭代的学习。

**学习任务：**

-   前向分步算法
-   负梯度拟合
-   损失函数
-   回归
-   二分类，多分类
-   正则化
-   优缺点
-   sklearn参数
 
<!-- more -->

GDBT 基本思想是根据当前模型损失函数的负梯度信息来训练新加入的弱分类器， 然后将训练好的弱分类器以累加的形式结合到现有模型中。

采用决策树作为弱分类器的Gradient Boosting算法被称为GBDT，有时又被称为MART（Multiple Additive Regression Tree）。GBDT中使用的决策树通常为CART。

GBDT中每一棵树学的是之前所有树结论和的残差， 这个残差就是一个加预测值后能得真实值的累加量。

举例子来说，假设对每个用户的年龄做出预测，例如用户A的真实年龄是25岁， 但第一棵决策树的预测年龄是22岁， 差了3岁， 即残差为3。 那么在第二棵树里我们把A的年龄设为3岁去学习， 如果第二棵树能把A分到3岁的叶子节点， 那两棵树的结果相加就可以得到A的真实年龄； 如果第二棵树的结论是5岁， 则A仍然存在−2岁的残差， 第三棵树里A的年龄就变成−2岁，继续学。 这里使用残差继续学习， 就是GBDT中Gradient Boosted所表达的意思。

# 前向分步算法
在GBDT的迭代中，假设我们前一轮迭代得到的强学习器（即当前模型）是 $f_{m-1}(x)$, 损失函数是 $L(y, f_{m-1}(x))$ , 我们本轮迭代的目标是找到一个CART回归树模型的弱学习器 $h(x:\alpha_{m})$，让本轮的损失函数 $L(y, f_{m}(x) =L(y, f_{m-1}(x)+ h(x:\alpha))$ 最小。也就是说，从前向后，每一步只学习一个基函数及其系数，逐步逼近优化损失函数。

输入：训练数据集 $T=\{(x_,y_1),(x_2,y_2), ...(x_N,y_N)\}$; 损失函数是 $L(y, f(x))$; 基函数集 ${h(x:\alpha)}$;

输出：加法模型$f(x)$.

1.  初始化$f_{0}(x)=0$
2.  For $m=1$ to $M$ do：

3.  通过极小化损失函数(如下公式1)得到下一颗决策树的参数:$\beta_{m},\alpha_{m}$.

$$(\beta_{m},\alpha_{m}) = arg\min \limits_{\beta,\alpha}\sum\limits_{i=1}^{N} L(y_i,f_{m-1}(x_i) + \beta h(x:\alpha))\tag{1}$$

4.  更新

$$f_{m}(x) = f_{tm-1}(x) + \beta_{m} h(x:\alpha_{m}))\tag{2}$$
5.  前三步循环结束即可得到加法模型：

$$f(x) = f_{M}(x) = \sum\limits_{m=1}^{M} \beta_{m} h(x:\alpha_{m}))\tag{3}$$



# GBDT 的负梯度拟合
若上述算法采用平方误差损失函数,

$$L(y, f(x)) = (y-f(x))^2\tag{4}$$

其损失变为

$$L(y,f_{m-1}(x) + \beta h(x:\alpha)
\\=[y-f_{m-1}(x) - \beta h(x:\alpha)]^2
\\=[r - \beta h(x:\alpha)]^2$$

这里，

$$r = y - f_{m-1}(x)\tag{5}$$

是当前模型拟合数据的残差（residual ). 所以， 对冋归问题的提升树算法来说，只需简单地拟合当前模型的残差.

提升树利用加法模型与前向分歩算法实现学习的优化过程. 当损失函数是平方损失和指数损失函数时， 每一步优化是很简单的. 但对一般损失函数而言， 往往每一步优化并不那么容易.

损失函数各种各样，怎么找到一种通用的拟合方法呢？

针对这一问题， Freidman 提出了梯度提升（gradient boosting) 算法. 这是利用最速下降法的近似方法， 其关键是利用损失函数的负梯度在当前模型的值来拟合本轮损失（即残差）的近似值。第m轮的第i个样本的损失函数的负梯度表示为下式(6):

$$r_{mi} = -\bigg[\frac{\partial L(y_i, f(x_i)))}{\partial f(x_i)}\bigg]_{f(x) = f_{m-1}\;\; (x)}\tag{6}$$




# GBDT 的算法思想
在每一轮迭代中，

1.  计算出当前模型在所有样本上的负梯度（可以作为残差的近似值）
2.  以该值为目标训练一个新的弱分类器进行拟合
3.  计算出该弱分类器的权重
4.  最终实现对模型的更新
 

Gradient Boosting算法的伪代码如下：

输入：训练数据集 $T=\{(x_,y_1),(x_2,y_2), ...(x_N,y_N)\}$; 损失函数是 $L(y, f(x))$; 基函数集 ${h(x:\alpha)}$;

输出：加法模型$f(x)$.

1.  初始化$f_{0}(x)= arg\min \limits_{\rho}\sum\limits_{i=1}^{N} L(y_i,\rho))$
2.  For $m=1$ to $M$ do：
-   (1) 计算负梯度

$$r_{mi} = -\bigg[\frac{\partial L(y_i, f(x_i)))}{\partial f(x_i)}\bigg]_{f(x) = f_{m-1}\;\; (x)}\tag{6}$$
    
-   (2) 通过极小化损失函数得到新的学习器的参数:下降的步长$\beta_{m}$和方向 $\alpha_{m}$.

$$\alpha_{m} = arg\min \limits_{\beta,\alpha}\sum\limits_{i=1}^{N} (r_{mi} - \beta h(x_i:\alpha))^2\tag{7}$$
-   (3) 计算新学习器的权重

$$\rho_m = arg\min \limits_{\rho}\sum\limits_{i=1}^{N} L(y_i,f_{m-1}(x_i) + \rho h(x_i:\alpha_{m}))\tag{8}$$

-   (4) 更新

$$f_{m}(x) = f_{m-1}(x) + \rho_{m} h(x:\alpha_{m}))\tag{9}$$
5.  循环结束即可得到加法模型：

$$f(x) = f_{M}(x) = \sum\limits_{m=1}^{M} \rho_{m} h(x:\alpha_{m}))\tag{10}$$

由于GBDT是利用残差训练的， 在预测的过程中， 我们需要把所有树的预测值加起来， 得到最终的预测结果。

# GBDT回归算法
输入是训练集样本$T=\{(x_,y_1),(x_2,y_2), ...(x_m,y_m)\}$， 最大迭代次数T, 损失函数L。

输出是强学习器f(x)

1) 初始化弱学习器

$$f_0(x) = \underbrace{arg\; min}_{c}\sum\limits_{i=1}^{m}L(y_i, c)$$

2) 对迭代轮数t=1,2,...T有：

a)对样本i=1,2，...m，计算负梯度

$$r_{ti} = -\bigg[\frac{\partial L(y_i, f(x_i)))}{\partial f(x_i)}\bigg]_{f(x) = f_{t-1}\;\; (x)}$$

b)利用$(x_i,r_{ti})\;\; (i=1,2,..m)$, 拟合一颗CART回归树,得到第t颗回归树，其对应的叶子节点区域为$R_{tj}, j =1,2,..., J$。其中J为回归树t的叶子节点的个数。

c) 对叶子区域j =1,2,..J,计算最佳拟合值

$$c_{tj} = \underbrace{arg\; min}_{c}\sum\limits_{x_i \in R_{tj}} L(y_i,f_{t-1}(x_i) +c)$$

d) 更新强学习器

$$f_{t}(x) = f_{t-1}(x) + \sum\limits_{j=1}^{J}c_{tj}I(x \in R_{tj})$$

3) 得到强学习器f(x)的表达式

$$f(x) = f_T(x) =f_0(x) + \sum\limits_{t=1}^{T}\sum\limits_{j=1}^{J}c_{tj}I(x \in R_{tj})$$

# GBDT分类算法
GBDT的分类算法从思想上和GBDT的回归算法没有区别，但是由于样本输出不是连续的值，而是离散的类别，导致我们无法直接从输出类别去拟合类别输出的误差。

为了解决这个问题，主要有两个方法，

1.  用指数损失函数，此时GBDT退化为Adaboost算法。
2.  是用类似于逻辑回归的对数似然损失函数的方法。也就是说，我们用的是类别的预测概率值和真实概率值的差来拟合损失。

本文仅讨论用对数似然损失函数的GBDT分类。对此我们又有二元分类和多元分类的区别。

## 二元GBDT分类算法
对于二元GBDT，如果用类似于逻辑回归的对数似然损失函数，则损失函数为：

$$L(y, f(x)) = log(1+ exp(-yf(x)))$$

　　　　其中$y∈\{−1,+1\}$.则此时的负梯度误差为:

$$r_{mi} = -\bigg[\frac{\partial L(y, f(x_i)))}{\partial f(x_i)}\bigg]_{f(x) = f_{m-1}\;\; (x)} = \frac{y_i}{1+exp(y_if(x_i))}$$
　　　　
对于生成的决策树，我们各个叶子节点的最佳负梯度拟合值为:

$$c_{mj} = \underbrace{arg\; min}_{c}\sum\limits_{x_i \in R_{mj}} log(1+exp(-y_i(f_{m-1}(x_i) +c)))$$
　　　　由于上式比较难优化，我们一般使用近似值代替

$$c_{mj} = \frac{\sum\limits_{x_i \in R_{mj}}r_{mi}} {\sum\limits_{x_i \in R_{mj}}|r_{mi}|(1-|r_{mi}|)}$$

　　　　除了负梯度计算和叶子节点的最佳负梯度拟合的线性搜索，二元GBDT分类和GBDT回归算法过程相同。

## 多元GBDT分类算法
多元GBDT要比二元GBDT复杂一些，对应的是多元逻辑回归和二元逻辑回归的复杂度差别。

假设类别数为K，则此时我们的对数似然损失函数为：

$$L(y, f(x)) = -  \sum\limits_{k=1}^{K}y_klog\;p_k(x)$$

其中如果样本输出类别为k，则$y_k=1$。第k类的概率$p_k(x)$的表达式为：

$$p_k(x) = \frac{exp(f_k(x))} {\sum\limits_{l=1}^{K} exp(f_l(x))}$$

集合上两式，我们可以计算出第t轮的第i个样本对应类别l的负梯度误差为

$$r_{mil} = -\bigg[\frac{\partial L(y_i, f(x_i)))}{\partial f(x_i)}\bigg]_{f_k(x) = f_{l, m-1}\;\; (x)} = y_{il} - p_{l, m-1}(x_i)$$

观察上式可以看出，其实这里的误差就是样本i对应类别l的真实概率和m−1轮预测概率的差值。

对于生成的决策树，我们各个叶子节点的最佳负梯度拟合值为

$$c_{mjl} = \underbrace{arg\; min}_{c_{jl}}\sum\limits_{i=0}^{m}\sum\limits_{k=1}^{K} L(y_k, f_{m-1, l}(x) + \sum\limits_{j=0}^{J}c_{jl} I(x_i \in R_{mj}))$$
　　　　
由于上式比较难优化，我们一般使用近似值代替

$$c_{tjl} =  \frac{K-1}{K} \; \frac{\sum\limits_{x_i \in R_{tjl}}r_{til}}{\sum\limits_{x_i \in R_{til}}|r_{til}|(1-|r_{til}|)}$$

除了负梯度计算和叶子节点的最佳负梯度拟合的线性搜索，多元GBDT分类和二元GBDT分类以及GBDT回归算法过程相同。

# GBDT常用损失函数

## 分类算法
损失函数一般有对数损失函数和指数损失函数两种:

1.  指数损失函数

$$L(y, f(x)) = exp(-yf(x))$$
　　　　
其负梯度计算和叶子节点的最佳负梯度拟合和Adaboost原理相同。

2.  对数损失函数，分为二元分类和多元分类两种，参见上文的GBDT分类算法。　　

## 回归算法
常用损失函数有如下4种:

1.  均方差，最常见的回归损失函数

$$L(y, f(x)) =(y-f(x))^2$$
2.  绝对损失，这个损失函数也很常见

$$L(y, f(x)) =|y-f(x)|$$
　　　　　　
对应负梯度误差为：

$$sign(y_i-f(x_i))$$

3.  Huber损失，它是均方差和绝对损失的折衷产物，对于远离中心的异常点，采用绝对损失，而中心附近的点采用均方差。这个界限一般用分位数点度量。损失函数如下：

$$L(y, f(x))= \begin{cases} \frac{1}{2}(y-f(x))^2& {|y-f(x)| \leq \delta}\\ \delta(|y-f(x)| - \frac{\delta}{2})& {|y-f(x)| > \delta} \end{cases}$$
　　　　对应的负梯度误差为：

$$r(y_i, f(x_i))= \begin{cases} y_i-f(x_i)& {|y_i-f(x_i)| \leq \delta}\\ \delta sign(y_i-f(x_i))& {|y_i-f(x_i)| > \delta} \end{cases}$$

4.  分位数损失。它对应的是分位数回归的损失函数，表达式为

$$L(y, f(x)) =\sum\limits_{y \geq f(x)}\theta|y - f(x)| + \sum\limits_{y < f(x)}(1-\theta)|y - f(x)|$$

其中θ为分位数，需要我们在回归前指定。对应的负梯度误差为：

$$r(y_i, f(x_i))= \begin{cases} \theta& { y_i \geq f(x_i)}\\ \theta - 1 & {y_i < f(x_i) } \end{cases}$$

对于Huber损失和分位数损失，主要用于健壮回归，也就是减少异常点对损失函数的影响。

# GBDT的正则化
和Adaboost一样，我们也需要对GBDT进行正则化，防止过拟合。GBDT的正则化主要有三种方式。

## 步长(learning rate)
第一种是和Adaboost类似的正则化项，即步长(learning rate)。定义为ν,对于前面的弱学习器的迭代

$$f_{k}(x) = f_{k-1}(x) + h_k(x)$$

如果我们加上了正则化项，则有

$$f_{k}(x) = f_{k-1}(x) + \nu h_k(x)$$

ν的取值范围为0<ν≤1。对于同样的训练集学习效果，较小的ν意味着我们需要更多的弱学习器的迭代次数。通常我们用步长和迭代最大次数一起来决定算法的拟合效果。

 
## 子采样
第二种正则化的方式是通过子采样比例（subsample）。取值为(0,1]。注意这里的子采样和随机森林不一样，随机森林使用的是放回抽样，而这里是不放回抽样。如果取值为1，则全部样本都使用，等于没有使用子采样。如果取值小于1，则只有一部分样本会去做GBDT的决策树拟合。选择小于1的比例可以减少方差，即防止过拟合，但是会增加样本拟合的偏差，因此取值不能太低。推荐在[0.5, 0.8]之间。

使用了子采样的GBDT有时也称作随机梯度提升树(Stochastic Gradient Boosting Tree, SGBT)。由于使用了子采样，程序可以通过采样分发到不同的任务去做boosting的迭代过程，最后形成新树，从而减少弱学习器难以并行学习的弱点。

 
## 正则化剪枝

针对于弱学习器（即CART回归树）进行正则化剪枝。就是决策树的正则化方法

# GBDT的优点和局限性

优点

1.  预测阶段的计算速度快， 树与树之间可并行化计算。
2.  在分布稠密的数据集上， 泛化能力和表达能力都很好， 这使得GBDT在Kaggle的众多竞赛中， 经常名列榜首。
3.  采用决策树作为弱分类器使得GBDT模型具有较好的解释性和鲁棒性，能够自动发现特征间的高阶关系， 并且也不需要对数据进行特殊的预处理如归一化等。

局限性

1.  GBDT在高维稀疏的数据集上， 表现不如支持向量机或者神经网络。
2.  GBDT在处理文本分类特征问题上， 相对其他模型的优势不如它在处理数值特征时明显。
3.  训练过程需要串行训练， 只能在决策树内部采用一些局部并行的手段提高训练速度。

# sklearn参数
在sacikit-learn中，`GradientBoostingClassifier`为GBDT的分类类， 而`GradientBoostingRegressor`为GBDT的回归类。两者的参数类型完全相同，当然有些参数比如损失函数loss的可选择项并不相同。

这些参数中，类似于Adaboost，我们把重要参数分为两类，
-   第一类是Boosting框架的重要参数，
-   第二类是弱学习器（即CART回归树）的重要参数。

下面我们就从这两个方面来介绍这些参数的使用。
## GBDT类库boosting框架参数
由于GradientBoostingClassifier和GradientBoostingRegressor的参数绝大部分相同，我们下面会一起来讲，不同点会单独指出。

1) n_estimators: 也就是弱学习器的最大迭代次数，或者说最大的弱学习器的个数。一般来说n_estimators太小，容易欠拟合，n_estimators太大，又容易过拟合，一般选择一个适中的数值。

    -   默认是100。在实际调参的过程中，我们常常将n_estimators和下面介绍的参数learning_rate一起考虑。

2) learning_rate: 即每个弱学习器的权重缩减系数ν，也称作步长，加上了正则化项，我们的强学习器的迭代公式为$f_{k}(x) = f_{k-1}(x) + \nu h_k(x)$。ν的取值范围为0<ν≤1。对于同样的训练集拟合效果，较小的ν意味着我们需要更多的弱学习器的迭代次数。通常我们用步长和迭代最大次数一起来决定算法的拟合效果。
    -   所以这两个参数n_estimators和learning_rate要一起调参。一般来说，可以从一个小一点的ν开始调参，默认是1。

3) subsample: 即我们在上文的正则化章节讲到的子采样，取值为(0,1]。注意这里的子采样和随机森林不一样，随机森林使用的是放回抽样，而这里是不放回抽样。如果取值为1，则全部样本都使用，等于没有使用子采样。如果取值小于1，则只有一部分样本会去做GBDT的决策树拟合。选择小于1的比例可以减少方差，即防止过拟合，但是会增加样本拟合的偏差，因此取值不能太低。
    -   推荐在[0.5, 0.8]之间，默认是1.0，即不使用子采样。

4) init: 即我们的初始化的时候的弱学习器，拟合对应原理里面的f0(x)，如果不输入，则用训练集样本来做样本集的初始化分类回归预测。否则用init参数提供的学习器做初始化分类回归预测。一般用在我们对数据有先验知识，或者之前做过一些拟合的时候，如果没有的话就不用管这个参数了。

5) loss: 即我们GBDT算法中的损失函数。分类模型和回归模型的损失函数是不一样的。

    -   对于分类模型，有对数似然损失函数"deviance"和指数损失函数"exponential"两者输入选择。默认是对数似然损失函数"deviance"。在原理篇中对这些分类损失函数有详细的介绍。一般来说，推荐使用默认的"deviance"。它对二元分离和多元分类各自都有比较好的优化。而指数损失函数等于把我们带到了Adaboost算法。

    -   对于回归模型，有均方差"ls", 绝对损失"lad", Huber损失"huber"和分位数损失“quantile”。默认是均方差"ls"。一般来说，如果数据的噪音点不多，用默认的均方差"ls"比较好。如果是噪音点较多，则推荐用抗噪音的损失函数"huber"。而如果我们需要对训练集进行分段预测的时候，则采用“quantile”。

6) alpha：这个参数只有GradientBoostingRegressor有，当我们使用Huber损失"huber"和分位数损失“quantile”时，需要指定分位数的值。默认是0.9，如果噪音点较多，可以适当降低这个分位数的值。

## GBDT类库弱学习器参数
由于GBDT使用了CART回归决策树，因此它的参数基本来源于决策树类，也就是说，和`DecisionTreeClassifier`和`DecisionTreeRegressor`的参数基本类似。

1) 划分时考虑的最大特征数max_features: 可以使用很多种类型的值，默认是"None",意味着划分时考虑所有的特征数；如果是"log2"意味着划分时最多考虑log2N个特征；如果是"sqrt"或者"auto"意味着划分时最多考虑N−−√个特征。如果是整数，代表考虑的特征绝对数。如果是浮点数，代表考虑特征百分比，即考虑（百分比xN）取整后的特征数。其中N为样本总特征数。
    -   一般来说，如果样本特征数不多，比如小于50，我们用默认的"None"就可以了，如果特征数非常多，我们可以灵活使用刚才描述的其他取值来控制划分时考虑的最大特征数，以控制决策树的生成时间。

2) 决策树最大深度max_depth: 默认可以不输入，如果不输入的话，默认值是3。一般来说，数据少或者特征少的时候可以不管这个值。如果模型样本量多，特征也多的情况下，推荐限制这个最大深度，具体的取值取决于数据的分布。
    -   常用的可以取值10-100之间。

3) 内部节点再划分所需最小样本数min_samples_split: 这个值限制了子树继续划分的条件，如果某节点的样本数少于min_samples_split，则不会继续再尝试选择最优特征来进行划分。 
    -   默认是2.如果样本量不大，不需要管这个值。如果样本量数量级非常大，则推荐增大这个值。

4) 叶子节点最少样本数min_samples_leaf: 这个值限制了叶子节点最少的样本数，如果某叶子节点数目小于样本数，则会和兄弟节点一起被剪枝。 默认是1,可以输入最少的样本数的整数，或者最少样本数占样本总数的百分比。
    -   如果样本量不大，不需要管这个值。如果样本量数量级非常大，则推荐增大这个值。

5) 叶子节点最小的样本权重和min_weight_fraction_leaf：这个值限制了叶子节点所有样本权重和的最小值，如果小于这个值，则会和兄弟节点一起被剪枝。 
    -   默认是0，就是不考虑权重问题。一般来说，如果我们有较多样本有缺失值，或者分类树样本的分布类别偏差很大，就会引入样本权重，这时我们就要注意这个值了。

6) 最大叶子节点数max_leaf_nodes: 通过限制最大叶子节点数，可以防止过拟合，默认是"None”，即不限制最大的叶子节点数。如果加了限制，算法会建立在最大叶子节点数内最优的决策树。
    -   如果特征不多，可以不考虑这个值，但是如果特征分成多的话，可以加以限制，具体的值可以通过交叉验证得到。

7) 节点划分最小不纯度min_impurity_split:  这个值限制了决策树的增长，如果某节点的不纯度(基于基尼系数，均方差)小于这个阈值，则该节点不再生成子节点。即为叶子节点 。
    -   一般不推荐改动默认值1e-7。


# 参考
-   《百面机器学习》
-   [梯度提升树(GBDT)原理小结](https://www.cnblogs.com/pinard/p/6140514.html)
-   [scikit-learn 梯度提升树(GBDT)调参小结](https://www.cnblogs.com/pinard/p/6143927.html)