---
layout: post
title: "使用 git 相关工具时一些记录"
subtitle: ""
catalog: true
author: "WangW"
header-style: text
tags: 
    - git
    - 工具
---

jupyter notebook 是一个十分常、优秀的软件，本文主要记录了关于该软件的常用方法。

git是目前世界上最先进的分布式版本控制系统，这篇文章主要记录经常用到的有关git的操作。<!--break-->

## 查看和修改用户名和地址

#### 查看用户名和地址

```bash
git config user.name
git config user.email
```

#### 修改用户名和地址

```bash
git config --global user.name "your name"
git config --global user.email "your email"
```

也可以直接用`git help`。

