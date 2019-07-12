---
layout: post
title: "anaconda 命令大全"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags: 
    - python
    - jupyter notebook
    - conda
---

Anaconda命令大全<!--break-->

![目录图](/img/in-post/2019/conda.png)

## 管理conda
- **conda --version**: 验证conda是否安装成功
- **conda update conda**: 更新conda到最新版本
- **conda -h**: 查看conda帮助信息
- **rm -rf ~/anaconda3**: 卸载conda

## 管理环境
- **conda create --name \<env_name\> \<package_name\>**: 创建新环境
- **source activate \<env_name\>**: 切换环境
- **source deactivate \<env_name\>**: 退出环境
- **conda env list == conda info --envs**: 显示已创建环境
- **conda create --name \<new_env_name\> --clone \<copied_env_name\>**:复制环境
- **conda remove --name \<env_name\> --all**: 删除环境

## 管理包
- **conda search --full-name \<package_full_name\>**:精确查找包
- **conda search \<text\>**: 模糊查找
- **conda list**: 获取当前环境中已安装的包信息
- **conda install --name \<env_name\> \<package_name\>**: 在指定环境中安装包
- **conda install \<package_name\>**: 在当前环境中安装包
- **conda remove --name \<env_name\> \<package_name\>**: 卸载指定环境中的包
- **conda remove \<package_name\>**: 卸载当前环境中的包
- **conda update --all == upgrade --all**：更新所有包

---

## 常见问题解决 （更新中）
### 添加Conda 环境至Jupyter Notebook中
``conda``已经停止自动将环境设置为``Jupyter``内核,需要手动为每个环境添加内核
1. 依赖前提，请自行判断  
```bash
source activate myenv
conda install pip
conda install ipykernel # or pip install ipykernel
```
2. 添加内核  
```bash
source activate myenv
python -m ipykernel install --user --name myenv --display-name "Python(myenv)"
```
``--name``就是Jupyter会在内部中使用该环境，``--display-name``在Jupyter Notebook菜单中看到的名称
3. 参考  
[http://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments](http://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments)

### Conda换源问题
1. 切换国内镜像源
```bash
conda config --show # 显示源
# 添加清华镜像 来自-https://mirror.tuna.tsinghua.edu.cn/help/anaconda/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/ 
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```
2. 切换到默认源
```bash
conda config --remove-key channels
```

### Anaconda国内镜像挂掉
清华，中科大的镜像之前因为Anaconda协议不可用。改用``pyenv-virtualenv``
```bash
python -m venv venv
source venv/bin/activate # Linux
activate venv/Scripts/activate # windows
```

### Conda 安装Pytorch
在win10安装Pytorch的时候，由于是国内网，在其官网源下载特别慢，而且anaconda镜像没有pytorch源，所以采用第三方源；听起来拗口，看步骤就好了。
1. 新建环境``conda create -n torch python=3.6``
2. [官网](https://pytorch.org/get-started/locally/)查看安装命令，例如``conda install pytorch-cpu torchvision-cpu -c pytorch``其中-c pytorch代表了在pytorch源上下载安装；
3. [清华源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)查看添加第三方（Pytorch）源的命令，例如``conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/``
4. 在新建的环境终端输入``conda install pytorch-cpu torchvision-cpu``,相比于第二步把官方源去掉了（//还用说吗//）
5. 下载完成；新建环境终端测试

**参考:**
- [https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
- [https://pytorch.org/get-started/locally/](https://pytorch.org/get-started/locally/)
- [https://blog.csdn.net/yuanzhoulvpi/article/details/86742729](https://blog.csdn.net/yuanzhoulvpi/article/details/86742729)

### Conda 安装Keras, Tensorflow
在安装Keras之前，首先需要确保安装了以下三个后端引擎：TensorFlow, Theano, CNTK;
- [TensorFlow installation instrctions](https://www.tensorflow.org/install/)
- [Theano installation instruction](http://deeplearning.net/software/theano/install.html#install)
- [CNTK installation instructions](https://docs.microsoft.com/en-us/cognitive-toolkit/setup-cntk-on-your-machine)

在此次安装中，由于TensorFlow官网没有介绍使用Conda安装方法，所有介绍一下如何使用conda 安装Tensorflow. 之后再装keras
```bash
# method A
conda create -n keras python=3.6
source activate keras
conda install tensorflow # or tensorflow-gpu

# method B
# conda create -n keras python=3.6 tensorflow

# install keras
conda install keras-gpu
```
**问题 1**: 安装之后发现报错``tensorflow.python.framework.errors_impl.InternalError: cudaGetDevice() failed. Status: CUDA driver version is insufficient for CUDA runtime version``原因翻译过来就是运行版本和驱动版本不匹配；  
```bash
# 查看GPU驱动程序版本 我的为388.13；
# 如果是win，打开终端cd进入C:\Program Files\NVIDIA Corporation\NVSMI.
# 亦可以将以上路径添加到环境变量中，方便经常查看gpu驱动程序状态
nvidia-smi 

# 我的keras环境中查看cuda版本
conda list # cudatoolkit=10.0; cudnn=7.6
```
知晓以上命令后，查看[英伟达官网](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)对应关系;**388.13对应cuda9.0**,由于GPU方程驱动安装复杂一下，因此我觉得改变cuda版本到9.0；
```bash
# 卸载cudatoolkit=10.0, cudnn 大概率会卸掉以他为依赖项的tensorflow等
conda remove cudatoolkit cudnn
# 安装9.0
conda install cudatoolkit=9.0
conda install cudnn
# 安装刚卸载的tensorflow
conda install tensorflow-gpu
conda install keras-gpu
```

总结：出现这个问题之后，除了上述解决方法，我们还可以更新gpu显卡驱动程序版本和cuda相匹配；最后==警告我们应当1. 先查看显卡驱动程序版本，2. 然后再装cudatoolkit，cudnn, 3. 最后装tensorflow-gpu, keras-gpu等。==

---

