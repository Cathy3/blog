---
title: LogisticRegression的sklearn参数说明
date: 2018-08-24 16:51:05
categories: 
		- 机器学习
tags:
        - 逻辑回归
        - sklearn
mathjax: true
---




 > *class* sklearn.linear_model.**LogisticRegression**(penalty=’l2’, dual=False, tol=0.0001, C=1.0, fit_intercept=True, intercept_scaling=1, class_weight=None, random_state=None, solver=’liblinear’, max_iter=100, multi_class=’ovr’, verbose=0, warm_start=False, n_jobs=1)


在scikit-learn中，主要是基于LogisticRegression模型来解决Logistic回归算法，其中，有两种不同的代价函数(cost function)：  
L1:  

$$ \min _ { w,c }\|w\| _ 1+C\sum _ { i=1 }^n log ( exp ( -y_ i(X _ { i }^ \mathrm { T }w+c))+1)$$

L2：  

$$ \min _{ w, c } \frac { 1 }{ 2 }w^ \mathrm { T }w +C \sum _{ i=1 }^nlog(exp(-y _i (X _{i}^ \mathrm { T }w+c))+1)$$

每个式子中前一项是**正则化项（Regularizer）**（包含w的范数），后一项是**损失函数（loss function）**，参数 **C** 控制了两者在最终的损失函数中所占的比重。  
求解w参数的方法根据L1/L2代价函数的不同，也存在不同的求解拟合参数的方法： 

<!-- more -->

| Case | Solver |
| ---- | --- |
| L2 penalty | “saga”,  “liblinear” , “lbfgs”,  “sag”,  “newton-cg” |
| L1 penalty | “saga”,  “liblinear” |
| Multinomial loss |“saga”, “lbfgs”,  “sag”,  “newton-cg” |
| Very Large dataset (n_samples) | “saga”, “sag” |

 - **penalty：**惩罚项，str类型，可选参数为l1和l2，默认为l2。
     - 用于指定惩罚项中使用的规范。加上约束，使得模型更不会过拟合(overfit)，可以获得泛化能力更强的结果
     - newton-cg、sag和lbfgs求解算法只支持L2规范。
     - L1规范假设的是模型的参数满足拉普拉斯分布，L2假设的模型参数满足高斯分布，
 - **dual：**对偶或原始方法，bool类型，默认为False。
     - 对偶方法只用在求解线性多核(liblinear)的**L2惩罚项**上。
     - 当样本数量>样本特征的时候，dual通常设置为False。
 - **tol：**停止求解的标准，float类型，默认为1e-4。
     - 就是求解到多少的时候，停止，认为已经求出最优解。
 - **c：**正则化系数λ的倒数，float类型，默认为1.0。必须是正浮点型数。
     - 像SVM一样，越小的数值表示越强的正则化。
 - **fit_intercept：**是否存在截距或偏差，bool类型，默认为True。
 - **intercept_scaling：** float类型，默认为1。
     - 仅在正则化项为"liblinear"，且fit_intercept设置为True时有用。
 - **class_weight：**用于标示分类模型中各种类型的权重.
     - 默认为None，也就是不考虑权重。可以输入一个字典或者'balanced'字符串。
     - 可以`{class_label:weight}`形式自己输入各个类型的权重。举个例子，比如对于0,1的二元模型，我们可以定义class_weight={0:0.9,1:0.1}，这样类型0的权重为90%，而类型1的权重为10%。
     - 也可以选择**balanced**，那么类库会根据训练样本量来计算权重。某种类型样本量越多，则权重越低，样本量越少，则权重越高。类权重计算方法如下：`n_samples/(n_classes *np.bincount(y))`。
     - `n_samples`为样本数，`n_classes`为类别数量，`np.bincount(y)`会输出每个类的样本数，例如 y=[1,0,0,1,1], 则 np.bincount(y)=[2,3]。
   那么class_weight有什么作用呢？ 在分类模型中，我们经常会遇到两类问题：
   **第一种是误分类的代价很高。**比如对合法用户和非法用户进行分类，将非法用户分类为合法用户的代价很高，我们宁愿将合法用户分类为非法用户，这时可以人工再甄别，但是却不愿将非法用户分类为合法用户。这时，我们可以适当提高非法用户的权重。
   **第二种是样本是高度失衡的**。比如我们有合法用户和非法用户的二元样本数据10000条，里面合法用户有9995条，非法用户只有5条，如果我们不考虑权重，则我们可以将所有的测试集都预测为合法用户，这样预测准确率理论上有99.95%，但是却没有任何意义。这时，我们可以选择balanced，让类库自动提高非法用户样本的权重。提高了某种分类的权重，相比不考虑权重，会有更多的样本分类划分到高权重的类别，从而可以解决上面两类问题。
 - **random_state：**随机数种子，int类型，可选参数，默认为None。
     - 仅在正则化优化算法为 sag, liblinear 时有用。
 - **solver：**优化方法选择。默认为liblinear。
     - 有五个可选参数，即newton-cg, lbfgs, liblinear, sag, saga。
     - **liblinear：**使用了开源的liblinear库实现，内部使用了坐标轴下降法来迭代优化损失函数。
     - **lbfgs：**L-BFGS算法，拟牛顿法的一种，利用损失函数二阶导数矩阵即海森矩阵来迭代优化损失函数。
     - **newton-cg：**也是牛顿法家族的一种，利用损失函数二阶导数矩阵即海森矩阵来迭代优化损失函数。
     - **sag：**即随机平均梯度下降(Stochastic Average Gradient descent)，是梯度下降法的变种，和普通梯度下降法的区别是每次迭代仅仅用一部分的样本来计算梯度，适合于样本数据多的时候。
     - **saga：**线性收敛的随机优化算法的的变重。 
     - **总结：**  
        - **liblinear适用于小数据集**，而sag和saga适用于大数据集因为速度更快。
        - 对于多分类问题，**只有newton-cg,sag,saga和lbfgs能够处理多项损失**，而**liblinear受限于一对剩余(OvR)**。意思就是用liblinear的时候，如果是多分类问题，得先把一种类别作为一个类别，剩余的所有类别作为另外一个类别。一次类推，遍历所有类别，进行分类。
        - **newton-cg, sag 和 lbfgs** 这三种优化算法时都需要损失函数的一阶或者二阶连续导数，因此不能用于没有连续导数的L1正则化，**只能用于L2正则化**。而 **liblinear 和 saga** **通吃L1正则化和L2正则化**。
        - 同时，sag每次仅仅使用了部分样本进行梯度迭代，所以当样本量少的时候不要选择它，而**如果样本量非常大，比如大于10万，sag是第一选择**。但是**sag不能用于L1正则化**，所以当你有大量的样本，又需要L1正则化的话就要自己做取舍了。要么通过对样本采样来降低样本量，要么回到L2正则化。
        - 逻辑回归有二元逻辑回归和多元逻辑回归。对于多元逻辑回归常见的有one-vs-rest(OvR)和many-vs-many(MvM)两种。而MvM一般比OvR分类相对准确一些。而liblinear只支持OvR，不支持MvM，这样**如果我们需要相对精确的多元逻辑回归时，就不能选择liblinear了**。也意味着如果我们需要相对精确的多元逻辑回归**不能使用L1正则化了**。
 - **max_iter：**算法收敛最大迭代次数，int类型，默认为10。
     - 仅在正则化优化算法为newton-cg, sag 和lbfgs才有用。
 - **multi_class：**分类方式选择参数，str类型，可选参数为ovr和multinomial，**默认为ovr**。
     - 如果是二元逻辑回归，ovr和multinomial并没有任何区别，区别主要在多元逻辑回归上。
     - **ovr**即前面提到的one-vs-rest(OvR)。OvR的思想是无论你是多少元逻辑回归，我们都可以看做二元逻辑回归。具体做法是，对于第K类的分类决策，我们把所有第K类的样本作为正例，除了第K类样本以外的所有样本都作为负例，然后在上面做二元逻辑回归，得到第K类的分类模型。其他类的分类模型获得以此类推。
     - 而**multinomial**即前面提到的many-vs-many(MvM)。MvM相对复杂，这里举MvM的特例one-vs-one(OvO)作讲解。如果模型有T类，我们每次在所有的T类样本里面选择两类样本出来，不妨记为T1类和T2类，把所有的输出为T1和T2的样本放在一起，把T1作为正例，T2作为负例，进行二元逻辑回归，得到模型参数。我们一共需要T(T-1)/2次分类。
     - 可以看出OvR相对简单，但分类效果相对略差（这里指大多数样本分布情况，某些样本分布下OvR可能更好）。而MvM分类相对精确，但是分类速度没有OvR快。
     - 如果选择了ovr，则4种损失函数的优化方法liblinear，newton-cg,lbfgs和sag都可以选择。**但是如果选择了multinomial,则只能选择 newton-cg, lbfgs 和 sag 了**。
 - **verbose:**日志冗长度，int类型。默认为0。就是不输出训练过程。
     - 1的时候偶尔输出结果，大于1，对于每个子模型都输出。
 - **warm_start：**热启动参数，bool类型。默认为False。
    - 如果为True，则下一次训练是以追加树的形式进行（重新使用上一次的调用作为初始化）。
 - **n_jobs：**并行数。int类型，默认为1。
     - 1的时候，用CPU的一个内核运行程序，
     - 2的时候，用CPU的2个内核运行程序。
     - 为-1的时候，用所有CPU的内核运行程序。

# 参考
 - [sklearn.linear_model.LogisticRegression-scikit-learn 0.19.2 documentation](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html#sklearn.linear_model.LogisticRegression.decision_function%20%E9%80%BB%E8%BE%91%E5%9B%9E%E5%BD%92)
 - [Logistic回归(Logistic Regression)算法笔记(二)-scikit learn](https://www.jianshu.com/p/bbdeb356057e)
 - [LogisticRegression - 参数说明](https://blog.csdn.net/jark_/article/details/78342644)

