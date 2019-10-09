---
layout: post
title: "Liunx中的一些问题总结"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags: 

    - Linux
---


目录:

[TOC]

<!--break-->

### SSH连接设置(服务器端和客户机端)

1. 下载ssh-server端，sudo apt-get install openssh-server
2. 可能要下载用户端，sudo apt-get install openssh-client
3. 更改用户端配置，sudo vim /etc/ssh/ssh_config;去掉文件中PasswordAuthentication yes前面的#号；
4. 更改服务器端配置，sudo vim /etc/ssh/sshd_config；把PermitRootLogin prohibit-password改成PermitRootLogin yes，保存退出；
5. 重启服务器，sudo /etc/init.d/ssh restart


### ssh第一次链接报错WAENING:REMOTE HOST IDEDTIFICATION HAS CHANGED
```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!

Someone could be eavesdropping on you right now (man-in-the-middle attack)!

It is also possible that a host key has just been changed.

The fingerprint for the RSA key sent by the remote host is

SHA256:这里每个人都不同

Please contact your system administrator.

Add correct host key in /Users/Anan/.ssh/known_hosts to get rid of this message.

Offending RSA key in /Users/Anan/.ssh/known_hosts:1

RSA host key for 这里是服务器的IP has changed and you have requested strict checking.

Host key verification failed.
```
会出现这些信息是因为，第一次SSH连接时，会生成一个认证，储存在客户端（也就是用SSH连线其他电脑的那个，自己操作的那个）中的known_hosts，但是如果服务器验证过了，认证资讯当然也会更改，服务器端与客户端不同时，就会跳出错误啦～因此，只要把电脑中的认证资讯删除，连线时重新生成，就一切完美啦～要删除很简单，只要在客户端输入一个指令
```bash
ssh-keygen -R +输入服务器的IP
```

### 安装TeamView
1. 下载安装包deb
2. dpkg -i ..deb
3. sudo apt install -f
4. sudo apt install ..deb

### 清华软件配置源
1. 如何使用请参照：[清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)
2. ``sudo apt update``更新apt源，**注意这里的update和upgrade的区别：update更新路径，相当于找到资源存放的位置；而upgrade更新软件，相当于软件管家自动升级已经安装好的软件。** 
3. 参考：[这里](http://www.bewindoweb.com/179.html)

### 在Ubuntu中添加和删除PPA的软件源
参考：
[在Ubuntu中添加和删除PPA的软件源](https://blog.csdn.net/lu_embedded/article/details/55803500)

### nvidia驱动问题
#### 方法一
成功[Ubuntu 16.04安装NVIDIA驱动](https://blog.csdn.net/CosmosHua/article/details/76644029)
#### 方法二
刚装的ubuntu系统，发现``nvidia-smi``命令无法使用，显示没有该命令.
1. 去[英伟达官网](https://www.nvidia.com/Download/driverResults.aspx/151568/en-us)查驱动型号，-430
```bash
sudo apt purge nvidia-*
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
sudo apt install nvidia-381
```
**安装速度超级慢，如何解决？**[解决方法参考](https://blog.csdn.net/u014561933/article/details/79958017)以及[这个](https://blog.csdn.net/CosmosHua/article/details/76644029)

参考：[如何在Linux上安装最新的NVIDIA驱动](https://10.linuxstory.net/how-to-install-latest-nvidia-drivers-in-linux/)

