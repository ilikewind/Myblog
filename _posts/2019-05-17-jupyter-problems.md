---
layout: post
title: "使用 jupyter notebook 时一些note"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags: 
    - python
    - jupyter notebook
---

jupyter notebook 是一个十分常、优秀的软件，本文主要记录了关于该软件的常用方法。

<!--break-->

## Conda 环境没有出现在Jupyter Notebook中

### 问题：

我安装了Anaconda，新建了一个虚拟环境```torch36```并在该环境中安装了`torch`包。 我可以在该环境中成功导入torch。但是在Jupyter Notebook 中无法识别我刚刚创建的新环境。

### 解决：

con'da 停止自动将环境设置为jupyter内核，需要自己手动为每个环境添加内核：

```python
source activate myenv
python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
```

可以参考：[http://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments](http://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments)

附录： 你应该能够安装 `nb_conda_kernels` 包装 `conda install nb_conda_kernels` 要自动添加所有环境，请参阅 [https://github.com/Anaconda-Platform/nb_conda_kernels](https://github.com/Anaconda-Platform/nb_conda_kernels)

## anacoda国内镜像已经挂了

清华，中科大等国内镜像已经不能使用。改用`pyenv-virtualenv`。

```
python3 -m venv venv
sourch venv/bin/activate

```

