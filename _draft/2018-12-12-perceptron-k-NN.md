---
layout: post
title: "详谈感知机、k-NN以及其实现方式"
subtitle: "perceptron-and k-NN"
author: "WangW"
header-style: text
tags: 
    - 机器学习
    - 统计学习
    - perceptron
    - k-NN
---

本文主要参考：《统计学习方法》-- 李航

- - -

**具体统计方法学习-链接（updating）**
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

## 1. 感知机（perceptron）

感知机是二分类的线性分类模型，其输入为实例的特征向量，输出为实例的类别，取-1和+1。感知机对应于输入空间（特征空间）中将实例划分为正负两类的分离超平面，属于判别模型。

### 1.1. 感知机模型

**定义 （感知机）** 假设输入空间（特征空间）是 $\chi \subseteq R^n$ ，输出空间是 $y=\{+1, -1\}$ 。输入$x\in \chi$ 表示实例的特征向量，对应于输入空间（特征空间）的点；输出$y\in y$ 表示实例的类别。由输入空间到输出空间的如下函数：

$$ f(x)=sign(w*x+b) $$

称为感知机，其中$$w$$和$b$为感知机模型参数，$w \in R^n$叫做权重（weight）或权值向量（weight vector），$b \in R$叫作偏置（bias）, $w * x$表示$w$ 和 $x$ 的内积。

感知机是一种线性分类模型，属于判别模型。感知机模型的假设空间是定义在特征空间中的所有线性分类模型（linear classification model）或线性分类器（linear classifier），即函数集合$\{f | f(x) = w* x + b\}$ 。

### 1.2. 感知机学习策略

#### 1.2.1. 数据集的线性可分

简单来说，就是给定一个数据集，如果存在某个超平面能够将数据集的正实例点和负实例点完全正确的划分到超平面的两侧。就称为数据集为线性可分数据集。

#### 1.2.2. 感知机学习策略

为了得到感知机参数 $w$ 和 $b$ ，需要确定一个学习策略，即定义（经验）损失函数并将损失函数极小化。

损失函数一个自然的选择是误分类点的总数。但是，这样的损失函数不是感知机参数的连续可导函数，不容易优化。损失函数的另一个选择是误分类到超平面的总距离，这是感知机所采用的。首先，写出输入空间 $R^n$ 中任一点 $x_0$ 到超平面的距离：

$$\frac{1}{\|w\|} |w*x_0 + b|$$ 

其次，对于误分类的数据$(w_i, y_i)$ 来说，

$$-y_i(w*x_i + b) > 0$$

成立。因此，误分类点$x_i$ 到超平面的距离就变成了：

$$-\frac{1}{\|w\|}(w*x_0 + b)$$

这样，假设误分类点集合M，那么所有误分类点到超平面的总距离为：

$$-\frac{1}{\|w\|}\sum_{x_i\in M}y_i(w*x_i+b)$$

不考虑$\frac{1}{\|w\|}$ ,就得到了感知机学习的损失函数。这个损失函数就是感知机学习的**经验风险函数**。

#### 1.2.3. 感知机学习算法的原始形式

简单说，就是随机选择一个误分类点，使其梯度下降。

**感知机学习算法的原始形式**：

输入：训练数据集 $T = \{(x_1,y_1),...,(x_n,y_n)\}$ ；学习率为$a$

输出：$w, b$ ；感知机模型

1. 选取初始值 $w_0,b_0$ 

2. 在训练集选取 $(x_i,y_i)$

3. 如果 $y_i(w*x_i+b)\leq0$

   $$ w \gets w+ay_ix_i$$

   $$b \gets b + ay_i$$

4. 转至 2 ，至到训练集没有误分类点。

#### 1.2.4 感知机学习算法的对偶形式

简单来说，就是在训练开始到训练结束，通过每一个实例被误分的个数，来进行梯度下降求解。

## 2. k 近邻法（k-NN）


