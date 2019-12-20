---
layout: post
title: "利用fMRI数据生成功能矩阵图谱--流程图记录"
subtitle: ""
catalog: true
author: "WangW"
head-style: text
tags:
    - fMRI
    - Brain network
---

记录在实际使用fMRI数据生成FC的流程。主要步骤如下：


<!--break-->

## 准备阶段
### 软件准备
- 安装Matlib软件，这里安装了matlib2017a;
- 下载SPM8/12, 这里选择了SPM12;
- 下载[gretna](https://www.nitrc.org/docman/view.php/668/2262/manual_v2.0.0.pdf)

```bash
## 打开Matlib, 找到工作路径

addpath(genpath('E:\gretna')) # 添加目标文件夹以及子文件夹到工作路径
gretna # 打开gretna软件
```
### 数据准备
其中fMRI数据大概有两种，一种是DICOM, 一种是INDI数据。

- 下载[DICOM数据](http://www.rfmri.org/DemoData)demo, 详细解释可以看链接；
- 下载[NIFTI数据](http://fcon_1000.projects.nitrc.org/), **未实验**


----

## Network Construction

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191218171143.png)

### R-fMRI预处理
为了一步一步学习处理过程中出现的文件，现一步一步处理。
上图可以看到gretna软件包含了如下几个预处理步骤：
- DICOM to NIfTI


首先点击上面的按钮(Select Path of Functional Dataset(e.g., FunRaw)), 将subjects实验对象加载到软件中。ps prefix是过滤选项。将原始数据加载到软件之后开始预处理

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20191218172740.png)
其中，步骤1选择原始数据。2框是参数选择。

#### DICOM to NIfTI

#### Remove First Images
原因是仪器开始时有个平衡过程，开始测得的图像不准确，因而舍弃前几个。
1. 选择Remove First Images 到Pipeline Option框中
2. 设置Time Point Number to Remove : 比如5或者10

#### Slice Timing
1. Slice Timing ---> Pipeline Option
2. 设置TR (s), Slice Order, Reference Slice.

#### Realign
空间矫正
1. Realign ---> Pipeline OPtion
2. 设置Num Passes

#### Normalize
为了组间比较，将个体大脑转换未标准脑
1. Normalize ---> Pipeline Option
2. 选择Normalizing Strategy  选择标准。
3. 设置Writing Options

#### Spatially Smooth
去噪  
参数设置：窗口大小

#### Temporally Detrend
去线性漂移

#### Temporally Filter
滤波，
1. Temporally Filter ---> Pipeline Options
2. TR(s) Band(HZ) 参数设置

#### Regress Out Covariates
为了减低脑部其它协变量的影响


**最后的结果**：

result:
``` 
├─GretnaFunNIfTI
│  ├─Sub_001
|  |  ├─rest.nii
│  ├─Sub_002
|  |  ├─rest.nii
│  └─Sub_003
|  |  ├─rest.nii

```

### Functional Connectivity Matrix Construction
1. 构建N * N 相关矩阵，N是脑区数数目
2. 还可以构建动态相关矩阵，基于滑动窗口方法。

#### result
[输出分析](https://likewind.top/2019/12/15/brain-network-fmri/#%E8%BE%93%E5%87%BA%E5%88%86%E6%9E%90)


---

## 探索常用fmri内部数据

