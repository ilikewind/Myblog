---
layout: post
title: "使用编程软件中记录的一些常用问题"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags: 
    - python
    - jupyter notebook
    - conda
---

jupyter notebook 是一个十分常、优秀的软件，本文主要记录了关于该软件的常用方法。

<!--break-->

## Conda 环境没有出现在Jupyter Notebook中

#### 问题

我安装了Anaconda，新建了一个虚拟环境```torch36```并在该环境中安装了`torch`包。 我可以在该环境中成功导入torch。但是在Jupyter Notebook 中无法识别我刚刚创建的新环境。

#### 解决

con'da 停止自动将环境设置为jupyter内核，需要自己手动为每个环境添加内核：

```python
source activate myenv
python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
```

可以参考：[http://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments](http://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments)

附录： 你应该能够安装 `nb_conda_kernels` 包装 `conda install nb_conda_kernels` 要自动添加所有环境，请参阅 [https://github.com/Anaconda-Platform/nb_conda_kernels](https://github.com/Anaconda-Platform/nb_conda_kernels)

## anacoda国内镜像已经挂了

清华，中科大等国内镜像已经不能使用。改用`pyenv-virtualenv`。

```bash
python3 -m venv venv
source venv/bin/activate ##Linux
activate venv/Scripts/activate  ## windows

```

#### pip 换源

```bash
### 临时换源
pip install tensorflow -i https://pypi.tuna.tsinghua.edu.cn/simple

```

```bash
## 永久换源
## Linux: 修改~/.pip/pip.conf
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple

## window: 修改增加一个目录 C:\User\xx\pip\pip.ini
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```



#### anacoda换源

```bash
conda config --show ##显示源
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
conda config --remove channels https://pypi.doubanio.com/simple/ ## option
```

#### anacoda换回默认源

```bash
conda config --remove-key channels
```

