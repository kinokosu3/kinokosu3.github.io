---
title: Docker学习纪录①
date: 2017-08-07 15:51:54
tags:
    - docker
---
***

### 干嘛要用docker

一种新兴的虚拟化方式

#### 更高效的运用系统资源

容器不需要硬件虚拟以及运行完整操作系统
<!--more-->
#### 更快的启动

直接运行于宿主内核

#### 一致的运行环境

这句话有毒。。。确保了应用运行环境一致性，从而不会再出现 “这段代码在我机器上没问题啊” 这类问题。

### 基本概念

- 镜像（image）
- 容器（container）
- 仓库（repository）

### 获取镜像

```shell
$ docker pull [选项] [Docker Registry地址]<仓库名>:<标签>
```

### 列出镜像

```shell
$ docker images
```

镜像ID是镜像的唯一标识，一个镜像可以对应多个标签

### 虚悬镜像

### 中间层镜像

```shell
$ docker images -a
```

这样我们会看到很多无标签的镜像，这些无标签的镜像很多都是中间层镜像，是其它镜像所依赖的镜像。

### 列出部分镜像

```shell
$ docker images ubuntu
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              16.04               f753707788c5        4 weeks ago         127 MB
ubuntu              latest              f753707788c5        4 weeks ago         127 MB
ubuntu              14.04               1e0c3dd64ccd        4 weeks ago         188 MB
```

docker images 还支持filter过滤器

### 特定格式列出镜像

```shell
$ docker images -q
```

## 以上都是杂谈，docker file是docker的核心




