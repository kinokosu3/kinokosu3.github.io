---
title: Docker学习纪录③
date: 2017-08-17 10:13:31
categories: 学习纪录
tags:
    - docker
---
***

### ENV 设置环境变量

格式有两种
* `ENV <key> <value>`
* `ENV <key1>=<value1> <key2>=<value2>`

RUN中使用环境变量记得加$

### EXPOSE 声明端口

格式为 `EXPOSE <端口1> [<端口2>] `

这只是一个声明，并不会因为运行就会开启这个端口的服务

在运行时候使用`-p <宿主端口>:<容器端口>`，这个是在运行时使用的，`-p`是映射宿主端口和容器端口，而`EXPOSE`仅仅是声明打算使用什么端口而已，并不会进行端口映射
<!--more-->
### WORKDIR 指定工作目录

格式为 `WORKDIR <工作目录路径>`

改变每一层的工作目录，而不是用`cd `

### USER 指定当前用户

暂缺

## 总结

`Dockerfile`是用来构建`docker`镜像的，每一个`RUN`为一层存储


