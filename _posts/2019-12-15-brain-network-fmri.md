---
layout: post
title: "基于fmri数据构建脑功能连接图"
subtitle: ""
catalog: true
author: "WangW"
head-style: text
tags:
    - fMRI
    - Brain network
---

最近用到了脑连接图，为了较快的达到这一目的，我采用了不求甚解的浏览资料，导致许多神经方面知识并不理解，稍后可能会补一些[认知神经科学](https://zh.wikipedia.org/wiki/%E8%AA%8D%E7%9F%A5%E7%A5%9E%E7%B6%93%E7%A7%91%E5%AD%B8)方面的知识。

基于fMRI数据构建脑功能连接图主要分为三个步骤：
1. 获取节点(脑分区)，有各种标准的分区方法比如AAL90, AAL116, Brodmann82, Power264等。只要选定了分区方法，那么就确定了节点；
2. 利用fMRI数据以及节点获取连接矩阵；
3. 通过连接矩阵和节点以及节点坐标获取脑图（可选）；

<!--break-->

# 介绍
高中生物就讲到过大脑里有亿级数量的神经元，神经元之间通过突触连接，以进行信息交流。因此大脑可以看作一个巨大的神经网络，神经元为节点，突触为边，如果我们可以清楚的了解这个巨大神经网络是怎么处理信息，怎么工作的，也就知道了大脑工作原理。

但就现在的科学技术来说，监视大脑所有的神经元以及突触连接是不可能的。但我们可以把一个大脑放到一个有网格标尺的三维空间中，测量每一个网格的核磁共振变化值。然后对大脑进行分区，如分维90个区域，每个区域包含多个网格，该区域的核磁共振值维其包含所有网格的平均值。可以把这90个区的每个区当作一个节点，如下图：

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191216183745.png)

分区方法有各种模板，主要有ALL, Brodmann等模板，只要确定了模板，那么也就唯一确定了所有节点。

fMRI数据是通过核磁共振扫描获取，扫描仪一般2s就可以对大脑进行一遍核磁共振扫描，得到一张三维图。如果没2s扫描一次，扫描多次就可以得到随时间变化的一系列三维脑图，加上时间，共是四维数据。对于大脑的某个节点（固定前三维），可以得到一个时间序列。

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191216195253.png)

每一个结点都如此。对于某两个结点，可以计算它们时间序列的pearson相关系数，如果超过既设阈值说明它们是协同，有连接的边（当然不是物理存在）。对任两个结点都计算相关系数值，就可以得到连接矩阵，也就得到了边。

从上面的描述可以知道得到的图，其中的结点不是真实的神经元，而是人为进行的分区，其中的边也不是真实的突触，而是相关性或协同性定义的，因此称为功能连接网络。

rs-fMRI，rs为resting state即静息态，指的是让被试处在不做任何任务时，采集fmri值。当然，在研究人做某些特定任务时的大脑活动，可以采集对应的fmri值。但实际上，即使人不做任何任务（静息态），脑也在不断活动，而做某些任务时某些连接更强烈，因此使用静息态研究基本的功能连接更合理些。但即使处在静息态，也无法得知人脑在想什么，或在想像做什么任务，这也是静息态的缺点。

# rs-fMRI数据及预处理
## fMRI数据
fmri数据主要有两种存储格式，DICOM和NIFTI。DICOM，文件后缀为 .dcm ，仪器在采集数据时并非直接同时得到三维空间所有网格点的值，而是移动切平面逐层测量，DICOM，把每一层都使用一个独立的文件存储，并用数字标记层数，组合这些层数才是一个三维数据。NIFTI，文件后缀为 .nii，直接为3维数据。

可以看看DICOM数据文件夹形式，这是在学习dparsf时里面看到的例子文件 [Demonstrational Data for Resting-State fMRI](http://www.rfmri.org/DemoData) ，看看里面的Funraw文件夹里的数据。

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191216195627.png)

一般文件都是这种形式，FunRaw中保存各个被试文件夹，Sub_001，Sub为subject，指001号被试，该文件夹下保存在该被试的4维数据。

NIFTI数据，每个被试的数也是用sub_0012等形式命名的文件夹保存，里面直接是个 .nii 文件，实际 .nii 里面也是多个文件，每个文件为一个时间点的三维脑数据，但合并成一个.nii文件。可以在 [这里](http://fcon_1000.projects.nitrc.org/fcpClassic/FcpTable.html) 下载得到，文件一般很大达到G以上。

## fMRI预处理
1. **将DICOM文件转为NIFTI 数据**。如果获得的数据是NIFTI就不用该步。
2. **移除前n个时间点数据(remove first time points)**。这是因为仪器开始时有个平衡过程，即开始时测得的图像也不准，故舍弃前几个。
3. **时间层校正(slice timing)**。每次采集一个3D脑图数据都是逐层扫描，且花了2s左右，这样一个3D脑图上的数据并不所有都是数据均同时采集，而是不同层有时间偏移。而计算结点相关时的时间序列是希望两个时间序列是相时的。因此做校正。
4. **头动校正(realign)**。被试试验时，难免不自主头发生轻微晃动，使得空间数据偏移。像拍照抖动一样。因此做校正。
5. **空间标准化(normalize)**。每个的脑部形状都是略有不同的，因此我们把数据变换到标准的脑空间中。虽然测试不同的脑，但都是放到同一标准脑中计算。
6. **平滑化(smooth)**。为了降低信噪比。
7. **去线性漂移(detrend)**。
8. **滤波(filter)**。
9. **协变量回归(regression out the covariables)**。为了降低脑部其它协变量的影响。
10. **Scrubbing**。

一般的软件中都包含了预处理过程。

# 构建脑连接网络
已现存很多能够自动化构建脑连接网络的软件，比如gretna, dparsf等。

## gretna构建脑连接网络简介
- [gretna手册](https://www.nitrc.org/docman/view.php/668/2262/manual_v2.0.0.pdf)

gretna软件主要包含5大模块：

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191216201720.png)

构建脑连接网络主要仅仅使用了第一个模块，该模块介绍如下：
> **FC Matrix Construction:** This section allows researchers to 1) perform R-fMRI data
preprocessing, including volume removal, slice timing, realignment, spatial normalization, spatial
smoothing, detrend, temporal filtering and removal of confounding variables by regression; and 2)
construct static or dynamic region of interest (ROI)-based functional connectivity matrices.

### 输出分析
最后会发现工作目录出现，GretnaSFCMatrixR 文件夹，里面为

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191216202712.png)

第一个是节点文件，第三个是被试001号的脑连接矩阵。具体分析这两个文件夹，

结点文件中的结点属性，前三3为三维空间坐标，第4列为结点类别这里均为1，第5列为结点大小，第6列其实是结点名称这里没写。如下所示：
```txt
-39.5807	-5.71401	51.108	1	1.000000	-
40.2365	-8.38623	52.3802	1	1.000000	-
-19.2128	34.7426	42.474	1	1.000000	-
20.9405	30.7998	43.9931	1	1.000000	-
-17.8767	47.25	-12.8836	1	1.000000	-
17.7395	47.6141	-13.6109	1	1.000000	-
-34.268	32.3246	35.7804	1	1.000000	-
36.5444	32.998	34.1722	1	1.000000	-
-31.9333	50.4556	-9.33333	1	1.000000	-
32.7245	52.3776	-10.5816	1	1.000000	-
-49.3067	13.1779	20.2178	1	1.000000	-
49.3539	14.8219	21.4632	1	1.000000
```

边文件的连接矩阵

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191216203019.png)


## dparsf介绍
- [dparsf官网文档以及教程](http://www.rfmri.org/)


# 脑连接网络可视化(脑图)
使用了BrainNet Viewer软件。该软件和gretna都来自同一个社区驱动的合作实验室-[NITRC](https://www.nitrc.org/)

- [BrainNet Viewer官网](https://www.nitrc.org/projects/bnv/)



---



Next:

- [认知神经科学概念理解](https://zh.wikipedia.org/wiki/%E8%AA%8D%E7%9F%A5%E7%A5%9E%E7%B6%93%E7%A7%91%E5%AD%B8)