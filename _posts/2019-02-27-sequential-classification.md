---
layout: post
title: sequential modeling of deep features for breast cancer histopathological image classification 论文阅读
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags: 
    - 文献阅读
---

文献链接：[Sequential Modeling of Deep Features for Breast Cancer Histopathological
Image Classification](http://openaccess.thecvf.com/content_cvpr_2018_workshops/papers/w44/Gupta_Sequential_Modeling_of_CVPR_2018_paper.pdf)

---

**摘要**：计算机化的组织病理学图像自动分类方法有助于减少病理学家的人工观察工作量。近年来，深度网络也引起了人们对组织病理学图像分析的关注。然而，**现有的方法在探索多层特征以改进分类方面却现有关注。**本文认为考虑多层特征是很重要的，因为图像中的不同区域在不同的放大倍数下可能包含不同层次上有用的鉴别信息。考虑到深度学习中各层之间存在依赖关系，提出了一种利用微调DenseNet提取的多层深度特征的序列框架。<!--break-->只有当层为该层传递了预先定义的截止置信度时，层才能对该样本做出决策。否则，该样本将被传递到下一层。结果表明，该框架比通常使用的最高层特征具有更好的性能。另外也表明，当显式地考虑时，中低层特征也携带有用的鉴别信息。

## 1 What: 这篇文章要解决什么问题？

- 设计计算机辅助乳腺癌（BC）组织病理学图像分类系统；
- 利用多层特征提高乳腺癌组织病理学图像的分类性能；

## 2 Why: 这篇文章为什么要解决这个问题？解决这个问题有什么意义？

1. 手工BC病理学图像的分类费时费力，费用昂贵。解决这个问题，有助于提高病理医生的诊断效率，解放劳动力...
2. 深度学习在医学图像领域的应用较新，现有的研究还没有广泛探讨显式考虑深度多层特征以提高分类性能的好处。

## 3 How: 这篇文章如何解决这个问题？

本文利用预先训练的CNN网络（DenseNet）作为图像特征提取器。在多阶段设置中，样本在特定阶段具有很高分类置信度标签的时候，该样本将在这一阶段确定其标签。否则，样本将传递到下一阶段。**本文主要考虑了底层、中层和高层特征对分类性能的共同影响。**下面将具体分析该方法！

### 3.1 特征学习

在这一阶段，我们考虑所提到方法使用的架构，讨论了包含特征提取的特征学习，也讨论了使用XGBoost进行降维和分类。

#### 3.1.1 DenseNet architecture

有关DenseNet的介绍请看：

1. CSDN简介：[DenseNet算法详解](https://blog.csdn.net/u014380165/article/details/75142664) , 
2. 论文：[Densely Connected Convolutional Networks](https://arxiv.org/pdf/1608.06993.pdf)，
3. 代码的github链接：<https://github.com/liuzhuang13/DenseNet> 
4. MXNet版本代码（有ImageNet预训练模型）: <https://github.com/miraclewkf/DenseNet> 

本文采用预先训练的DenseNet-169 (165-conv+3-trainsition+1-classification)，如下图所示。我们冻结该网络结构的前30层网络，因为他们学习通用的特性。并根据特定的应用重新训练剩余的网络层。在微调中，根据DenseNet的输入尺寸重新调整图像的大小。使用（1664-1000-2）替换最后的全连接层，并在average pooling和dense layer之间增加0.4的dropout层.

![DenseNet-169](/img/in-post/2019/DenseNet.png)

#### 3.1.2 Classification with layer-wish features

有关XGBoost的介绍请看：

1. CSDN简介：[通俗理解kaggle比赛大杀器xgboost](https://blog.csdn.net/v_july_v/article/details/81410574)
2. 论文：[XGBoost: A Scalable Tree Boosting System](https://www.kdd.org/kdd2016/papers/files/rfp0697-chenAemb.pdf)

不同的卷积层产生的特征的维度很高，因此为了使用这些特征进行分类，主成分分析法用于降维。在本文中，使用XGBoost进行降维和分类。

![XGBoost](/img/in-post/2019/XGBoost.png)

### 3.2 顺序框架

![](/img/in-post/2019/sequential framework.png)

本段详细介绍了顺序框架，如上图所示，已下介绍了程序的流程。

- 每一次上的分类器都利用从DenseNet的卷积层中提取的特征进行训练。
- 对每一个部分，我们设置一个截断的分类置信（概率）来决定样本是否会通过下一层。
- 由于XGBoost提供给定类中对样本进行分类的概率，因此每个样本都有两个值。我们计算概率差，如果差值大，则该层对该样本具有较高的置信度，如果超过了某一层的截止置信度，则该层本身将对该样本进行决策。因此，样本不会进一步传播，这减少了后续层的混乱。
- 每个部分的置信分界点是这样确定的：对应于低级特征的部分具有高分界点，对应于高级特征具有低分界点。这样做的动机是，**低级特征不捕获任何特定的模式**，因此，在这些层上分类的样本即使具有这样的一般特征，也应该具有明显的区分能力。为了选择合适的分界点，我们选择合适的两个值，并将其分为相同的**165部分**。（通过验证集获得两个值）
- 对于没有足够置信跨层的样本，我们称之为**混淆样本**（Ambiguous samples）决策有两种方式
  - 平均概率
  - 投票

### 3.3 DenseNet 分类框架基线

在训练的神经网络中，采用最后一层完全连通层的特征作为基线。**毕竟低级中级高级特征混合使用的优势，该基线只使用了高级特征**

## 4 What: 这篇文章解决这个问题得到了什么样的结论？

本文测试数据使用[Break-His](https://www.researchgate.net/profile/Fabio_Spanhol/publication/283513314_A_Dataset_for_Breast_Cancer_Histopathological_Image_Classification/links/5768680208aef6cdf9b4067d/A-Dataset-for-Breast-Cancer-Histopathological-Image-Classification.pdf). 图像大小包含752×582， 700×460。算**正常图像尺寸**， 并非whole slide images. 如下图所示：

![](/img/in-post/2019/dataset description.png)

**评价标准采用了病人识别率（Patient recognition rate, PRR）**

### 4.1 采用深度特征的序列框架

![](/img/in-post/2019/result1.png)

![](/img/in-post/2019/result2.png)

从Table 2, 3, 4, 5中可以看出：

- 对于40×，底层网络结构对分类性能有明显的作用
- 由于400×结构更加具体，所以，更高的层可以更好的捕捉到它们。
- 对于中等放大图像，所有的层的贡献相似
- 使用一个卷积层产生的特征通常比使用深度多层特征分类性能差
- 在大多数情况下，较低的卷积层的性能比较深的卷积层差
- 在大多数情况下，深度卷积层比完全连接的层表现出更好的性能。这说明卷积层的作用是为分类建立更多的判别特征
- 通过Xgboost将信息融合到多层网络的性能优于基线DenseNet。相对于基线的准确性的提高，说明了在多层框架中，中低层特征和高层特征的作用
- 考虑到表2中的歧义百分比，我们注意到不满足任何层截断的歧义样本所占的比例非常小。无论如何，即使这些问题也会得到进一步解决。模糊度的计算方法与患者评分的计算方法相同。然而，这里我们不取所有病人的平均值，而是对模糊度评分进行加法。

### 4.2 不同方法的比较

![](/img/in-post/2019/result3.png)

==**提出的序列框架下的多层深度特征融合确实优于基线网络，也优于仅使用最高层次特征的分类。**==

