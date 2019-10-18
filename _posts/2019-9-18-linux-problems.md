---
layout: post
title: "Linux中的一些问题总结"
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

### SSH内网穿透
我是采用了花生壳这个软件，一个月1GB免费流量，够用；
具体步骤如下：
1. 下载花生壳客户端并安装，下载[链接](https://hsk.oray.com/download/)
2. 安装phddns.deb软件，请启用，其中具体包含以下参数``phddns``查看参数
3. ``phddns start``服务端开启该软件，你将会看到一个SN码和remote网站，记住SN码并登录该账号
4. 添加映射。
5. SSH登录。注意端口问题。

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20190919232235.png)

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20190919232313.png)

![](https://raw.githubusercontent.com/learnroad/image_host/master/2019/20190919232352.png)

### 源码安装卸载以及软件安装文件夹选项
- [Linux软件编译安装和相关目录介绍](https://www.jianshu.com/p/a71fbf51c4ff)

### 给普通用户添加sudo权限
#### 方法一
```bash
# 查看可以用sudo命令的群组和用户，发现了sudo群组
sudo vim /etc/sudoers

# 查看群组
sudo vim /etc/group

# 将普通用户添加到sudo群组中
sudo gpasswd -a hateyou sudo
```

#### 方法二
既然可以打开``/etc/sudoers``文件，发现其中包含可以使用的群组，那么我们也可以直接添加用户或者群组到该文件中，使得可以使用sudo权限.
**这个是更加个性化**，比如你想让某个普通用户仅仅可以使用/etc/init.d/nagios脚本重启的权限，这样的话用该方法比较方便。

#### 附加
最常用的配置：
用户 登录的主机=（可以变换的身份） 可以执行的命令
比如：
root ALL=(ALL) ALL
root用户可以在任意主机上切换到任意用户，执行任意命令。

ossadm ALL=(dbuser) NOPASSWORD:ALL
ossadm用户可以在任意主机上，切换到dbuser用户，执行任意命令，并且不用输入密码。
默认是需要输入当前已登陆的用户的密码的。也就是说，这条命令，如果没有NOPASSWORD这个关键字，ossadm切换到dbuser是需要输入ossadm用户本身的密码的。

ossuser ALL=(root) NOPASSWORD:/opt/sudobin2/pkgscript.sh
ossuser可以在任意主机上，切换为root用户，执行/opt/sudobin2/pkgscript.sh命令，并且不用输入密码。