---
layout: post
title: "深度学习与网络生物学"
subtitle: "Deep Learning for Network Biology"
catalog: true
author: "WangW"
header-style: text
tags: 

    - Deep Learning
    - Network Biology
---
《[Deep Learning for Network Biology](<http://snap.stanford.edu/deepnetbio-ismb/>)》教程记录。

[TOC]


# 前言

生物网络是强大的资源，可用于发现从单细胞到种群水平的生物系统中的相互作用和新兴特性。网络方法已被多次使用，以合并和扩增来自单个基因的信号，并已导致生物学上的非凡发现，包括药物发现，蛋白质功能预测，疾病诊断和精密医学。此外，这些方法在发现新生物学方面显示出广泛的实用性，并为湿实验室实验中的新发现做出了贡献。

因此希望能够利用机器学习以及深度学习研究生物网络，或者说网络吧。

## 机器学习

[构建机器学习一般步骤](https://likewind.top/2019/08/08/dl-base2/)

## 网络以及生物网络的介绍

### 网络以及生物网络

**网络** 指的是具有节点和节点间关系组成的连为一体的结构(单元)

**生物网络[^1]**指的是适用于生物系统的网络。生物网络为生态学、进化学和生理学研究中的发现的子单元连接提供了数学表示。生物信息学的关注焦点逐渐从个别基因、蛋白质和搜索算法转向大规模网络，并产生了各种“组”（-omes）如生物组（biome）、相互作用组（interactome）、基因组或蛋白质组。这些理论研究表明，生物网络与其他网络（例如因特网或社交网络）共享许多特征。其中**生物学网络主要包含以下几种：**


1. 蛋白-蛋白相互作用网络
2. 基因调控网络 （DNA-蛋白质相互作用网络）
3. 基因共表达网络 (转录本-转录本关联网络)
4. 代谢网络
5. 信号网络
6. 神经网络
7. 食物网

[^1]: 维基百科-生物网络

### 网络中的一般预测任务

- Node classification - Predict a type of a given node
- Link prediction - Predict whether two nodes are linked
- Community detection - Identify densely linked cluster of nodes
- Network similarity - How similar are two nodes or networks

### 深度学习在网络实践中的挑战

主要就是深度学习模型一般被设计为处理2D grid structure (图像等)或者linear 1D structure (自然语言等)数据。**网络具有任意尺寸以及复杂拓扑结构稍显复杂** 

### 教程主要内容

看到上面介绍挑战，就可以很容易猜到教程主要内容了，应当包括如何将网络结构转换为常见的结构以此来适应现代深度学习网络模型、采用图神经网络模型还有异构网络(可以浅显理解为整合不同结构网络，这个完全没想到)。

Node embeddings
:	Map nodes to low-dimensional embeddings

Graph neural networks
:	Deep learning approaches for graphs

Heterogeneous networks
:	Embedding heterogeneous networks

# 节点嵌入

![]({{ site.url }}/img/in-post/2019/network-set.png)

节点嵌入的一般步骤：(1) 设计节点映射嵌入空间编码器；(2) 设计图中节点相似性函数; (2) 优化参数使得
$$
similarity(u, v) \approx Z_v^T Z_u
$$
其中Z 为函数（1）获得。
**嵌入方法：** （1）许多方法采用基于节点相似性设计嵌入，比如node2vec, DeepWalk, LINE, struc2vec；（2）也有许多基于别的概念，比如基于节点是否相连、节点是否分享相同邻节点、节点是否有相似的局部网络结构等；

## Adjacency-based similarity

![]({{ site.url }}/img/in-post/2019/adjacency-1.png)

![]({{ site.url }}/img/in-post/2019/adjacency-2.png)

![]({{ site.url }}/img/in-post/2019/adjacency-3.png)

而从[构建机器学习一般步骤](https://likewind.top/2019/08/08/dl-base2/#%E6%9E%84%E5%BB%BA%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E7%AE%97%E6%B3%95)中我们知道构建机器学习的配方不外乎为**特定数据集、代价函数、优化方法、模型**。那么在这个Adjacency-based similarity的特征嵌入中，应当如何直观理解呢？

- 数据集应当为需要低维度嵌入的原始网络；
- 代价函数如图采用的是均方误差
- 优化方法有（1）公式解，比如奇异值分解、KKT等；（2）数值优化，比如SGD等；
- 模型就是节点--> 低纬度向量的编码器。

**问题：** 在图中，$$ A_{u,v} $$ 是怎么计算的？为什么这样认定？~~我认为就像[节点嵌入中嵌入方法](# 节点嵌入) 一样考虑节点相似性或者其他概念来定义这个A权重函数。~~ 只考虑邻接 matrix (在该语义下怎么理解matrix?)

## Random walk approaches

### 基础

由于我对无监督学习方面接触较少，为了尽快了解随机漫步方法，我搜索了一些重要的名词解释。

- [特征学习](https://cloud.tencent.com/developer/article/1086245)
- [稀疏自编码器](https://blog.csdn.net/jiede1/article/details/76681371)
- [随机过程-随机游走](https://zhuanlan.zhihu.com/p/28515637)

### Random walk approaches

![]({{ site.url }}/img/in-post/2019/walk1.png)

![]({{ site.url }}/img/in-post/2019/walk2.png)

![]({{ site.url }}/img/in-post/2019/walk3.png)

![]({{ site.url }}/img/in-post/2019/walk4.png)

![]({{ site.url }}/img/in-post/2019/walk5.png)

![]({{ site.url }}/img/in-post/2019/walk6.png)

![]({{ site.url }}/img/in-post/2019/walk7.png)

![]({{ site.url }}/img/in-post/2019/walk8.png)

![]({{ site.url }}/img/in-post/2019/walk9.png)

随机漫步可以看作是马尔可夫链的特例。它在  t_i +1   的状态仅仅由它在时刻  t_i  的状态以及它随机选择的方向所决定，和过去的状态无关。

### 理解

$$
\max _{f} \sum_{u \in V} \log \operatorname{Pr}\left(N_{\mathrm{R}}(u) | \mathbf{z}_{\mathrm{u}}\right)
$$

这个里面  N_R  和  Z_u  是在同一个维度吗？如何理解？

$ N_{R} $就是节点的临近网络短距离随机漫步的状态集合，  $  Z_{u} $ 就是节点的嵌入。式子中Pr模型是为了“通过z拟合Nr区域”，也就是表征。可以看上面的图

$$
\mathbf{Z}_{u}^{\top} \mathbf{Z}_{v} \approx \begin{array}{c}{\text { Probability that } u} \\ {\text { and } v \text { co-occur in a }} \\ {\text { random walk over }} \\ {\text { the network }}\end{array}
$$
怎么证明这一句话？比如，前提条件是约等于号左边应当不大于1, 如何保证？我认为是可以表示，而不是约等于。

## Biomedical applications

![]({{ site.url }}/img/in-post/2019/bio_application.png)





---

**待更新...**

# 图神经网络

## 图神经网络基础

## 图卷积神经网络

## 生物医学应用



# 异构网络

## Shallow embeddings for het nets

### OhmNet

### Metapath2vec

## Deep embeddings for het nets

### Decagon



# 总结

## 建议和演示

## 未来展望和结论



