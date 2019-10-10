---
layout: post
title: "Linux初始环境配置"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
categories:
    - Linux
tags: 

    - Linux
---


目录:

[TOC]

虽然我们可以备份Linux相关的配置以及软件，然而万事总有万一，因此掌握裸机配置环境也是必不可少。不可否认的是，所有的问题确实都可以谷歌搜索解决，但是对这些问题进行一个总结对之后再次遇到该问题将会节省很多的时间。<!--break-->

## 基本配置

当裸机刚装上Linux(以Ubuntu为例)系统之后，我们需要一些基本的配置来满足我们的日常操作。列表如下：

### SSH连接设置

服务器是多人使用，一般每个人登录服务器是使用的 **SSH**。成功的使用该命令需要一些配置。需要在服务器端和客户机端做一些配置，参考[这里](https://likewind.top/2019/09/18/linux-problems/#ssh%E8%BF%9E%E6%8E%A5%E8%AE%BE%E7%BD%AE%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E5%92%8C%E5%AE%A2%E6%88%B7%E6%9C%BA%E7%AB%AF)

### 软件源配置

Ubuntu的软件源默认在国外，因此在更新软件过程中，更新速度很慢。为了解决这一问题，采用清华镜像软件源，其中具体的配置请参考[这里](https://likewind.top/2019/09/18/linux-problems/#%E6%B8%85%E5%8D%8E%E8%BD%AF%E4%BB%B6%E9%85%8D%E7%BD%AE%E6%BA%90)

### NVIDIA驱动安装

在我的日常工作中，需要用到英伟达的驱动，如何安装最新驱动请参照[这里](https://likewind.top/2019/09/18/linux-problems/#nvidia%E9%A9%B1%E5%8A%A8%E9%97%AE%E9%A2%98)

## 特殊配置

除了基本的一些配置，为了工作需求，还要做一些专用的配置。

### anaconda配置

- 将conda环境添加到Jupyter Notebook中，参考[这里](https://likewind.top/2019/05/17/jupyter-problems/#%E6%B7%BB%E5%8A%A0conda-%E7%8E%AF%E5%A2%83%E8%87%B3jupyter-notebook%E4%B8%AD)
- conda下载慢，如何换源，请参考[这里](https://likewind.top/2019/05/17/jupyter-problems/#conda%E6%8D%A2%E6%BA%90%E9%97%AE%E9%A2%98)
- 安装Pytorch，参考[这里](https://likewind.top/2019/05/17/jupyter-problems/#conda-%E5%AE%89%E8%A3%85pytorch)
- 安装Tensflow, Kears, 参考[这里](https://likewind.top/2019/05/17/jupyter-problems/#conda-%E5%AE%89%E8%A3%85keras-tensorflow)

### git 配置

- git clone 国内网速慢，请参考[这里](https://likewind.top/2019/05/25/git-problems/#git-%E4%B8%8B%E8%BD%BD%E7%BD%91%E9%80%9F%E6%85%A2)

