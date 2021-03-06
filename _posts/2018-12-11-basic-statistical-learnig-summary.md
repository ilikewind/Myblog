---
layout: post
title: "基本统计方法概论总结"
subtitle: "Basic statistical learning summary"
catalog: true
author: "WangW"
header-style: text
tags: 
    - Machine Learning
    - Statistical Learning
---
本文主要参考：《统计学习方法》-- 李航

---

**具体统计方法学习--链接（updating）**

- 感知机
- k 近邻法
- 朴素贝叶斯法
- 决策树
- 逻辑斯谛回归与最大熵模型
- 支持向量机
- 提升方法
- EM算法
- 隐马尔可夫模型
- 条件随机场 

<!--break-->

### 1. 基本统计学习方法特点的概括总结  

本文主要介绍10种基本的统计学习方法，包括感知机、k 近邻法、朴素贝叶斯法、决策树、逻辑斯谛回归与最大熵模型、支持向量机、提升方法、EM 算法、隐马尔可夫模型、条件随机场。

表1. 10种统计学习方法特点概括总结

|  方法  |  适用问题  |  模型特点  | 模型类型 |          学习策略          |   学习的损失函数   |   学习算法   |
| :----: | :--------: | :--------: | :------: | :------------------------: | :----------------: | :----------: |
| 感知机 | 二分类问题 | 分离超平面 | 判别模型 | 极小化误分类点到超平面距离 | 误分点到超平面距离 | 随机梯度下降 |
|k 近邻法|多类分类，回归|特征空间，样本点|判别模型|无|无|无|
|朴素贝叶斯法|多类分类|特征与类别的联合概率分布，条件独立假设|生成模型|极大似然估计，极大后验概率估计|对数似然损失|概率计算公式，EM 算法|
|决策树|多类分类，回归|分类树，回归树|判别模型|正则化的极大似然估计|对数似然损失|特征选择，生成，剪枝|
|逻辑斯谛回归与最大熵模型|多类分类|特征条件下类别的条件概率分布，对数线性模型|判别模型|极大似然估计，正则化的极大似然估计|逻辑斯谛损失|改进的迭代尺度算法，梯度下降，拟牛顿法|
|支持向量机|二类分类|分离超平面，核技巧|判别模型|极小化正则化合页损失，软间隔最大化|合页损失|序列最小最优化算法（SMO）|
|提升算法|二类分类|弱分类区的线性组合|判别类型|极小化加法模型的指数损失|指数损失|前向分布加法算法|
|EM算法|概率模型参数估计|含隐变量概率模型|无|极大似然估计，极大后验概率概率估计|对数似然损失|迭代算法|
|隐马尔科夫模型|标注|观测序列与状态序列的联合概率分布模型|生成模型|极大似然估计，极大后验概率估计|对数似然损失|概率计算公式，EM 算法|
|条件随机场|标注|状态序列条件下观测序列的条件概率分布，对数线性模型|判别模型|极大似然估计，正则化极大似然估计|对数似然损失|改进的迭代尺度算法，梯度下降，拟牛顿法|

### 2. 统计学习三要素

统计学习方法都是由模型、策略和算法构成的，即统计学习方法由三要素构成，可以简单地表示为：

<center>方法 = 模型 + 策略 + 算法</center>

下面主要简单论述监督学习中的统计学习三要素。非监督学习、强化学习也同样拥有这三要素。所以，构建一种统计学习方法就是确定具体的统计学习三要素。

#### 2.1. 模型

统计学习首先要考虑的问题是学习什么样的模型。在监督学习过程中，模型就是所要学习的条件概率分布或者决策函数。模型的假设空间（hypothesis space）含所有可能的条件概率分布或决策函数。例如，假设决策函数是输入变量的线性函数，那么模型的假设空间就是所有这些线性函数构成的函数集合。假设空间的模型一般有无穷用个。

假设空间用 $ f $ 表示。假设空间可以定义为决策函数的集合。

$$ F = \lbrace f|Y=f(X) \rbrace {, (1)} $$

其中，$X $ 和 $Y $ 是定义在输入空间 $ \chi $ 和输出空间 $ y $ 上的变量。

#### 2.2. 策略

有了模型的假设空间，统计学习接着要考虑的是按照上面样的准则学习或选择最优的模型。**统计学习的目标在于从假设空间中选取最优的模型**。

首先需要了解 **损失函数** 与 **风险函数** 的概念。损失函数度量模型一次预测的好坏，风险函数度量评价意义下模型预测的好坏。

##### 2.2.1. 损失函数和风险函数

 监督学习问题是在假设空间 $ F $ 中选取模型$ f $作为决策函数，对于给定的输入 $ X $  , 有 f(X) 给出相应的输出 $f(X)$ , 这个输出的预测值与真实值Y可能一致也可能不一致，用一个损失函数（loss function）或者代价函数（cost function）来度量预测错误的程度。损失函数是 $ f(X) $ 和 $ Y $ 的非负实值函数，记作 $ L(Y, f(X)) $ 。

统计学习常用的损失函数有以下几种：

1. 0-1 损失函数（0-1 loss function)
2. 平方损失函数（quadratic loss function）
3. 绝对损失函数（absolute loss function）
4. 对数损失函数（logarithmic loss function）或对数似然损失函数（log-likelihood loss function）

损失函数值越小，模型就越好。由于模型的输入、输出 $ (X, Y) $ 是随机变量，遵循联合分布 $ P(X, Y) $ , 所以损失函数的期望是理论上模型 $ f(X) $ 关于联合分布 $ P(X, Y) $ 的平均意义下的损失，称为风险函数（risk function）或期望损失（expected loss）。

学习的目标就是选择期望风险最小的模型。然而，由于联合分布式未知的，期望损失不能直接计算。事实上，如果知道联合分布，也就知道条件分布了，也就不需要学习了。

所以也就引入了经验风险（empirical risk）或经验损失（empirical loss）。当数据足够多的时候，根据大数定律，经验损失趋于期望损失。所以一个自然的想法就是用经验损失代替期望损失。然而，现实中，数据的训练样本数目有限，甚至很小，所以用经验损失估计期望损失的效果常常不理想，要对经验损失进行一定的矫正。这就关系到了监督学习的连个基本策略：**经验损失最小化** 和 **结构损失最小化**。

##### 2.2.2. 经验损失最小化与结构经验损失最小化

1. 经验损失最小化

   当数据足够时，直接最小化经验损失。

2. 结构经验最小化（structural risk minimization, SRM）

   是为了防止过拟合提出的，等价于正则化。

#### 2.3. 算法

算法是指学习模型的具体计算方法。统计学习基于训练数据集，根据学习策略，从假设空间中选择最优模型，最后需要考虑用什么样的计算方法求解最优模型。

这时，统计学习问题就变成了最优化问题，统计学习的算法成为求解最优化问题的算法。如果最优化问题有显式的解析解，这个最优化问题就比较简单。但通常不存在，这就需要用数值计算的方法求解。如何保证找到全局最优解，并使求解的过程非常高效，就成为一个重要的问题。

### 后记

本文主要浅谈了基本的统计学习方法，以及统计学习方法的三要素。


