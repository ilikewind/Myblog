---
layout: post
title: "Attention-based Deep Multiple Instance Learning 论文阅读"
subtitle: "Attention-based Deep Multiple Instance Learning"
author: "WangW"
header-style: text
tags: 
    - 文献阅读
---

文献链接：[Attention-based Deep Multiple Instance Learning](https://arxiv.org/pdf/1802.04712.pdf)

---

### What: 这篇文章要解决什么问题？

为了解决MIL的可解释性；

### Why: 这篇文章为什么要解决这个问题？解决这问题有什么意义？

- 病理图像在现在发挥着越来越重要的作用。
- 有监督学习已经在医学图像领域展现出来了优异的效果，然而有监督学习所需要的标注数据是十分费事，并且也十分昂贵，更需要专业的知识来解决标记问题；
- 另外，有经验的医学专家有可能对有争议的病例持有不同的意见；
- 无监督学习（学习数据不需要专家标记）指出了一个光明的方向，然而在临床上没有什么应用，于是，介于有监督学习和无监督学习的半监督学习有望解决此类问题。而半监督学习仅仅需要粗粒度的标记，学习细粒度的分类和分割。
- 多示例学习的迅速发展，然而，大多数多示例学习最大的问题是需要使用预先指定的特征（手工特征），于是，CNN和多示例结合也就自然而然的被提了出来
- 在14年有MIL+CNN的文章用来分析医学图像，然而，局限性在于需要使用补丁图像。

1. 解决这个问题，可以使分割效率更高，是一个端对端的流程。

### How: 这篇文章如何解决了这个问题？

![](https://github.com/ilikewind/Myblog/blob/master/img/in-post/2018-12/CNN%2BMIL%E6%A1%86%E6%9E%B6.png)

#### 基线（our baseline）

构建了一个端到端的MIL方法作为基线，进行图像到图像的学习和预测，其中MIL公式支持从图像级标签中自动学习像素级分割。

交叉熵代价函数：

$$l_{mil} =  \sum_{i} \lgroup I(Y_i=1)logY_i^* + I(Y_i=0)log(1-Y_i^*)\rgroup \tag{1} $$

其中，$Y_i^*$是图像级的预测，$Y_i$是图像级的标签；另外$Y_{ik}^*$是第$i$个图像的第$k$个像素的预测。

通常情况下，$Y_i^* = max_kY_{ik}^*$，然而，会导致（1）导数不连续，（2）不能同时考虑所有的实例。所以，我们采用Generalized Mean(GM)作为我们的$softmax $ function，：

$$Y_i^* = (\frac{1}{|X_i|}\sum_{k=1}^{|X_i|}Y_{ik}^*)^{\frac{1}{r}} \tag{2}$$

因此可以用链式法则求出导数（The chain rule of differentiation）

$$\frac{\partial l_{mil}}{\partial Y_{ik}^*} = \frac{\partial l_{mil}}{\partial Y_i^*}\frac{\partial Y_i^*}{\partial Y_{ik}^*} \tag{3}$$

#### 约束深度弱监督（Constrained Deep Weak Supervision）

- 深度弱监督

在基线标准上增加了面积约束$a_i$。

假设有$T$个侧边输出层（side-output layers）,其中$t=\{1, 2,3,...,T\}$.

每一个侧边输出层的交叉熵损失函数是：

$$l_{mil}^{(t)} =  \sum_{i} \lgroup I(Y_i=1)logY_i^t + I(Y_i=0)log(1-Y_i^t)\rgroup\tag{4}$$

第t层侧边输出层的损失函数定义为：

$$l_{side}^{(t)}(\theta, w) = l_{side}^{(t)}(\theta, w)\tag{5}$$ 

于是乎，目标函数定义为;

$$l_{side}(\theta, w) = \sum_{t=1}^Tl_{side}^{t}(\theta, w)\tag{6}$$

- 约束深度弱监督

在注释过程中，专家给出了癌区域相对大小的粗略估计。对每幅图像中所有实例的总体“正”的度量计算为：

$$v_i = \frac{1}{|X_i|}\sum_{k=1}^{|X_i|}Y_{ik}^*\tag{7}$$

我们将面积约束定义为L2损失：

$$l_{ac}=I(Y_i=1)\sum_i(v_i - a_i)^2\tag{8}$$

自然地，损失函数就变成了：

$$l_{side}^{(t)}(\theta, w) \leftarrow l_{mil}^{(t)}(\theta, w) +  \eta_tl_{ac}^{(t)}(\theta, w)\tag{9}$$

目标函数，依旧是公式6所描述的。

- 融合模型

为了充分利用跨尺度的预测，将侧边输出预测融合到一起得到一个融合层。融合层的输出定义为：

$$Y_{fuse}^* = \sum_{t=1}^Ta_tY_{side}^{t}\tag{10}$$

于是乎，融合层的损失函数为：

$$l_{fuse}(\theta,w)=l_{side}^{(fuse)}(\theta, w)+\eta_{fuse}l_{ac}^{fuse}(\theta, w)\tag{11}$$

**最终的损失函数定义为侧边依旧融合层损失函数之和**：

$$l(\theta, w)=l_{side}(\theta, w)+l_{fuse}(\theta,w)\tag{12}$$

最后，利用算法，最小化我们的损失函数：

$$(\theta, w)^* = argmin_{\theta,w}l(\theta,w)\tag{13}$$

### What: 这篇文章解决这个问题得到了什么样的结论？

本文开发了一种深度弱监督下的端到端的框架，对组织病理学图像进行图像到图像的分割。为了更好的学习多尺度问题，我们在方法中国开发了深度弱监督。此外，还引进了区域约束，以寻求额外的弱监督信息。实验表明，我们的方法在具有挑战性的大规模组织病理学图像上得到了最先进的结果。我们提出的方法范围很广，可以广泛应用于医学成像和计算机视觉等领域。
