---
layout: post
title: "PyTorch系列 (一): pytorch使用总览"
subtitle: "PyTorch 1: How to use pytorch"
author: "WangW"
header-style: text
tags: 
    - PyTorch
---

**文章首发于：**[WangW Blog](https://likewind.top)，转载请注明出处。
**PyTorch系列：**

- [PyTorch系列(一) - PyTorch使用总览](https://likewind.top/2019/01/17/Pytorch-introduction/)
- [PyTorch系列(二) - PyTorch数据读取](https://likewind.top/2019/02/01/Pytorch-dataprocess/)
- [PyTorch系列(三) - PyTorch网络构建](https://likewind.top/2019/02/15/Pytorch-networks/)
- [PyTorch系列(四) - PyTorch网络设置](https://likewind.top/2019/02/19/Pytorch-setting/)


<!--break-->

**参考：**

1. [PyTorch documentation](https://pytorch.org/docs/stable/data.html)
2. [PyTorch 码源](https://github.com/pytorch/)

**目录：**

[TOC]

深度学习框架训练模型时的代码主要包含数据读取、网络构建和其它设置三个方面 。基本上掌握这三个方面就可以较为灵活地使用框架训练模型。

## 1 数据读取
[pytorch 数据预处理，包分析以及应用实例](https://likewind.top)
**具体步骤主要包含：**
**1. 定义``torch.utils.data.Dataset``数据接口**
**2. 使用``torch.utils.data.DataLoader``将数据接口封装成数据迭代器**

数据读取部分包含如何将你的数据转换成PyTorch框架的Tensor数据类型。当使用PyTorch构建好网络模型之后，将数据读取进去的**接口**全部与**torch.utils.data**有关，而在其接口下主要分为了**dataset**、**dataloader**、**sampler**和**distributed**脚本。``torch.utils.data``建立了模型与数据之间的桥梁。
除此之外，在**torchvision**中的**datasets**就是使用继承了``torch.utils.data.Dataset``基类。而**torchvison.trainsforms**则一些图片的预处理函数。综上所述，PyTorch的与数据相关的接口函数库可以总结为：

- torch.utils.data
	- dataset
	- dataloader
	- sampler
	- distributed

下面是视觉有关的包，其中继承``Dataset``的例子以及图像预处理例子被**标粗**

- torchvision
	- **datasets**
	- models
	- **transforms**
	- utils

## 2 网络构建

**如何自定义网络结构？在PyTorch中，构建网络的类都是基于torch.nn.Modele这个基类进行的。**也就是说所有的网络结构的构建都可以通过进程该类来实现，包括torchvision.models接口中的模型实现类也是继承这个基类进行重写的。自定义网络结构可以参考models中的模型。

**PyTorch框架中提供了一些方便使用的网络结构和预训练模型：torchvision.models** 该接口可以直接导入指定的网络结构，并且可以选择是否用预训练模型初始化导入的网络结构。

**如果要用某预训练模型为自定义的网络结构进行参数初始化，**可用torch.load接口导入预训练模型，然后调用自定义的网络结构对象的load_state_dict方式进行参数初始化，具体可以参考[https://github.com/miraclewkf/MobileNetV2-PyTorch](https://github.com/miraclewkf/MobileNetV2-PyTorch)项目中的train.py脚本中if args.resume条件语句。

**与网络构建相关库可以总结为：**

- torch.nn
	- **modules文件夹**
	- backends文件夹
	- _functions文件夹
	- parallel文件夹
	- utils文件夹
	- cpp
	- **functional**
	- **init**
	- ...
	
- torchvision
	- models
	- ...


## 3 网络设置
**损失函数**通过torch.nn包实现，比如torch.nn.CrossEntropyLoss()接口表示交叉熵等。

**优化函数**通过torch.optim包实现，比如torch.optim.SGD()接口表示随机梯度下降。更多优化函数可以查看PyTorch官方文档。

**学习率策略**通过torch.optim.lr_scheduler接口实现，比如torch.optim.lr_scheduler.StepLR()接口表示按指定epoch数减少学习率。更多的学习率变化策略可以看官方文档。

**多GPU训练**通过torch.nn.DataParallel接口实现，比如：``model = torch.nn.DataParallel(model, device_ids=[0, 1])``表示在gpu0和1上训练模型。

- torch.optim
	- adadelta
	- adagrad
	- adam
	- adamax
	- asgd
	- lbfgs
	- lr_scheduler
	- optimizer
	- rmsprop
	- rprop
	- sgd
	- sparse_adam
- torch.nn
	- **module文件夹**
	- **function**
	- **_function文件夹**
	- ...


## 4 其它设置

按照以上步骤，可以较为简单的使用PyTorch框架进行训练，然而，还有一些其它设置以及包需要说明；

- torch.autograd
- torch.multiprocessing
- torch.distributed
- torch.distribution
- torch.jit

