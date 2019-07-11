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
# 添加清华镜像
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/ 
conda config --set show_channel_urls yes
conda config --remove channels https://pypi.doubanio.com/simple/ ## option
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

---

