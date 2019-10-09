---
loyout: post
title: "在ubuntu下opensldie的安装，更新以及卸载问题"
subtitle: "Install uninstall update openslide in ubuntu"
catalog: true
author: "Wangw"
header-style: text
tags: 
    - openslide
    - ubuntu
---

**首发在**[WangW Blog](https://likewind.top) 

###  引出问题

在处理whole slide images时，出现`Slide/Mask dimension does not match , X_slide / X_mask : 98304 / 1536, Y_slide / Y_mask : 103936 / 2048`的问题。

<!--break-->

### 问题原因

openslide版本问题，在openslide=\=3.4.1中更新了这个问题，所以要安装`openslide==3.4.1`。**最想吐槽的是，尼玛啊，camelyon比赛官方评价标准代码中用 $2^n$ 倍数关系控制whole slide image多分辨率问题，但是openslide==3.4.1之前的版本不适用这个倍数关系**，这才导致多分辨率下长宽倍数关系不一致，我觉得很LOW！

### 解决方法

大体分为这6个步骤：

- 查看openslide版本
- 卸载openslide
- 安装openslide==3.4.1依赖项
- 安装openslide==3.4.1
- 查看安装是否成功
- 安装openslide-python

**查看openslide版本：**通过`openslide-show-properties --version`查看目前安装的版本，如果不是3.4.1版本，请接着往下看，如果是，就退出本页面就OK了。

**卸载openslide:**通过`sudo apt-get purge --auto-remove python-openslide`连带其依赖项完全卸载干净，以及删除相应的文件，麻烦确保使用该命令删除干净，不然你会安装两个版本的openslide, 那等下安装`openslide-python`，它会依赖调用那个版本的openslide只能听天由命了！！这时候，你可以通过查看版本，来查看openslide有没有卸载完成，**如果没有**，请想别的办法，卸载之后再继续...

**安装openslide==3.4.1:**

**方法一：**

#### 更新：

在之后的安装过程中，我发现最新版的openslide已经放到了ubuntu的软件源，所以安装更加简单，仅需要：

```bash
sudo apt-get install openslide-tools
sudo apt-get install python-openslide
pip install openslide-python
```



**方法二：**

1. 首先进入[openslide官网](https://openslide.org/download/) 下载源文件

2. 解压文件，编译

   ```
   cd openslide-3.4.1
   ./configure
   make
   sudo make install 
   ####
   到上面基本安装完成，如在./configure报错，可能就是缺少依赖项，请使用：
   sudo apt-get install * 来安装依赖项，在进行./configure。。。
   ```

**查看安装是否完成：**`openslide-show-properties --version`看一下版本

**安装openslide-python:**`pip install openslide-python`



### 后记

有什么问题欢迎留言！

