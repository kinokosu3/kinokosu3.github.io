---
title: 搭建git服务器
date: 2017-08-10 10:26:52
tags:
    - git
categories: 备忘录
---
***

因为自己的代码写的太难看，不太敢老是丢在github上，但是我又想有一个远程仓库，然后手上有一个树莓派，我们就可以用这个来搭建一个git服务器

> 适合Ubuntu和debian

### 安装Git

```shell
$ sudo apt-get install git
```

### 建立一个git用户

我们以后要用推送到远程仓库，需要使用这个账户使用git服务

```shell
$ sudo useradd git
```
<!--more-->
### 生成公钥

在用户文件夹路径下（/home/git/）输入命令

```shell
$ ssh-keygen –t rsa
```

一路回车

然后命令`ls -a` 查看是否有.ssh文件夹，进入

### 配置无密码ssh登录

在/home/git/.ssh输入命令

```shell
$ sudo vim authorized_keys
```

你会发现有两个文件id_rsa.pub和id_rsa，id_rsa.pub是公钥文件

创建authorized_keys文件，然后将你的登录的机器的公钥放进去，你的公钥一样在.ssh文件夹的id_rsa.pub里

然后我们重启试试能不能用git用户无密码登录

### 初始化git仓库

先选定一个目录作为git仓库，然后创建一个名为sample.git的git仓库

```shell
$ sudo git init --bare sample.git
```

在git里，--bare是创建一个裸仓库，服务器上的git仓库都以git结尾，然后我们修改权限

```shell
$ sudo chown -R git:git sample.git
```

### 推送和克隆

如果我们要推送一个仓库到这个裸仓库，或者以后需要远程推送，我们就需要使用push命令

如果是第一次推送，首先我们要关联一个远程库，也就是之前的裸仓库

```shell
$ git remote add origin git@地址:git仓库路径
```

添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

然后我们就可以把本地库的内容推送的远程库上：

```shell
$ git push -u origin master
```

使用git push命令，其实是把当前分支master推送到远程上

由于我们这是第一次推送仓库，远程库是空的，所以我们加上了`-u`参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来

如果我们本体做了修改，我们就可以通过命令

```shell
$ git push origin master
```

把本地master分支的最新修改推送到github








