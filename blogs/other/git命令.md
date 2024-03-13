---
title: git 常用命令
date: 2024/02/29
tags:
 - git 
categories:
 - other
---

## 简介

在使用git的时候，可能会遇到问题，以下些都是常用的 git 命令，希望能帮助你节约查找命令所浪费的时间，将时间用在更加有用的地方。

## 详细命令

查看远程分支 `git branch -r`

查看本地分支 `git branch -l`

查看本地和远程分支 `git branch -a`

新建本地分支并同步远程分支`git checkout -b 本地分支 origin/远程分支`

丢弃本地全部修改`git checkout .`

删除本地分支`git branch -d 分支`

切换本地分支`git checkout 分支`

查看本地修改的部分`git status`

查看文件增减的部分`git  diff file.c`

强制重置本地文件和云端文件保持一致`git fetch`+`git reset --hard origin/分支`

将文件加到暂存区`git add 文件.c`

将文件从暂存区移除`git restore --staged 文件.c`

添加commit`git commit -m "内容"`

将commit同步到云端`git push origin HEAD:refs/for/分支`

查看本地没有提交的commit log`git log  branch_name  ^origin/branch_name`

查看已经合并的代码历史`git log`

查看已经添加了commit的文件`git show commitID`

在已经push代码后，修改代码（不改变commit title）`git commit --amend --no-edit`

更改id_rsa密码`ssh-keygen -f ~/.ssh/id_rsa -p`

查看本地ssh账户`git config --global --list`

创建全局账户的账户名`git config --global user.name "用户名"`

创建全局账户的邮箱`git config --global user.email "邮箱地址"`

生成密钥`ssh-keygen -t rsa -C "邮箱地址"`
