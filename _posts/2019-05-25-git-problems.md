---
layout: post
title: "git 命令大全"
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

### 前言
动画学习git基本操作：[https://learngitbranching.js.org/](https://learngitbranching.js.org/)

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

---
## git 基本操作
- **git init**: 将目录变成git可以管理的仓库
- **git add**: 将文件添加到仓库 -暂存区
- **git commit**: 将文件提交到仓库 - 仓库
- **git log --pretty=oneline**: 显示从最近到最远的提交日志（回到过去）
- **git reset --hard**: 版本回退
- **git reflog**: 记录命令（回到未来）
- **git status**: 查看工作状态
- **git diff HEAD --readme.md**: 查看工作区和版本库里最新版本的区别
- **git checkout --file**: 撤回工作区修改到上一次add 或 commit
- **git reset HEAD file**:撤回暂存区修改，和上面命令共同时使用更佳
- **git rm**: 删除

## 远程仓库
- **git remote add origin ...**: 添加远程仓库
- **git push**: 推送
- **git clone**: clone 仓库

## 分支管理
- **git checkout -b dev**: 创建``dev``分支，并切换到``dev``分支上
- **git branch**: 查看当前分支
- **git checkout master**: 切换到master主枝上
- **git merge dev*:  将dev上的工作成果合并到master分支上
- **git branch -d dev**: 删除dev分支
- **git log --graph**查看分支合并图
- **git merge --no-ff**: 不会fast forward，能查看分支
- **git stash**: 隐藏目前工作区，用来临时fix bug
- **git stash list**: 查看隐藏的工作区
- **git stash pop == git staxh apply + git stash drop**: 恢复工作区，并删除stash.
- **git remote -v**: 查看远端仓库信息
- 注意多人协作中的常用方式
- **git rebase**: 将本地未push的分叉提交历史整理成直线

## 标签管理
- **git tag v1.0**: 打标签
- **git tag -d v0.1**: 删除标签
- **git push origin v1.0**: 推送标签到远程
- **git push origin --tags**: 一次性推送所有未推送的本地标签到远端
- **git tag -d v0.9**: 先删除本地标签
- **git push origin :refs/tags/v0.9**: 后删除远端标签


