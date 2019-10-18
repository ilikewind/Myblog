---
layout: post
title: "Deep Learning for Network Biology 的一些思索和疑问"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags: 

    - Deep Learning
    - Network
---

[TOC]

**教程来自--**[ISMB 2018 Tutorial: Deep Learning for Network Biology](http://snap.stanford.edu/deepnetbio-ismb/)

该教程在第0部分简单介绍了网络以及分析网络的‘方法’，并简要说明了网络的特点：任意大小和复杂的拓扑结构。从而引出了现代处理网络较为常用的方式，**节点嵌入、图网络、异构网络**，第1，2，3章分别介绍了这三类方式。第四章主要是生物网络分析的建议以及未来的挑战，也给出了一些演示。

我将会记录一些要点、需要扩展的知识以及疑问。

<!--break-->

# 前言
主要介绍了分析网络的方法（倒不如说理解网络这种数据）：
- 预测节点类型，比如节点分类
- 预测两节点是否连接，比如链接预测
- 识别紧密的节点群，比如群落识别
- 节点间/网络间的相似性

## 问题及拓展
1. 网络这种数据是怎么样生成的？一个网络能表达什么？
2. 生物网络数据的挖掘以及获取。

**还未搜索相关资料**

----


# 节点嵌入
![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191018232205.png)

## Adjacency-based similarity


而从构建机器学习一般步骤中我们知道构建机器学习的配方不外乎为特定数据集、代价函数、优化方法、模型。那么在这个Adjacency-based similarity的特征嵌入中，应当如何直观理解呢？

- 数据集应当为需要低维度嵌入的原始网络；
- 代价函数如图采用的是均方误差
- 优化方法有（1）公式解，比如奇异值分解、KKT等；（2）数值优化，比如SGD等；
- 模型就是节点–> 低纬度向量的**编码器**。

### 问题及扩展
1. 忘掉怎么理解这个概率学上的模型了。比如 $ P_{model}(A | Z_1 Z_2) $

**查阅概率论书籍**

## Random walk approaches
仅仅改变了相似度方程。

### 问题及扩展
1. 如何理解以下两个公式？1)式和2)式的最后一项都表示了u,v出现在同一个随机漫步过程的概率？
2. 理解概率模型（同上查阅概率论书籍）。

$$
\mathbf{Z}_{u}^{\top} \mathbf{Z}_{v} \approx \begin{array}{c}{\text { Probability that } u} \\ {\text { and } v \text { co-occur in a }} \\ {\text { random walk over }} \\ {\text { the network }}\end{array}
$$

$$
\mathcal{L}=\sum_{u \in V} \sum_{v \in N_{R}(u)}-\log \left(\frac{\exp \left(\mathbf{z}_{u}^{\top} \mathbf{z}_{v}\right)}{\sum_{n \in V} \exp \left(\mathbf{z}_{u}^{\top} \mathbf{z}_{n}\right)}\right)
$$

----

# 图神经网络
由于上面讲的浅编码器的局限性：需要固定的参数、必须是固定的转换、无法体现节点的特性。因此需要讨论深度图编码器以及图神经网络。

$$
\operatorname{ENC}(v)=\begin{array}{l}{\text { multiple layers of non-linear }} \\ {\text { transformation of graph structure }}\end{array}
$$

（感觉像机器学习和深度学习或者说神经网络的关系了）

## Basics of deep learning for graphs
略

## Graph convolutional networks
略

### 扩展
- 查阅一些概率图相关知识
- [Relational inductive biases, deep learning, and graph networks](https://arxiv.org/pdf/1806.01261.pdf)
- 理解节点特征(网络数据理解)

# 异构网络
主要利用上面的内容来处理异构网络。异质网络处理起来就是注意一下层次结构就好了。

## Shallow embeddings for het nets
### OhmNet
### Metapath2vec
略

## Deep embeddings for het nets
### Decagon
略

#### 扩展
- 多尺度问题
- 如何处理层次结构以及常用的规范。