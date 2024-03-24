---
title: docker实践尝试
date: 2017-08-18 10:03:14
categories: 学习纪录
tags:
    - docker
---
***

### 基础命令（频繁使用）

* 查看镜像`docker images`


* 构建镜像`docker build -t`

	`-t`是标签的意思，格式是`<REPOSITORY > : <TAG>`，如果你不创建这个，就会创建一个你FROM的镜像的默认名字，比如你在dockerfile指定`FROM nginx`，然后再`docker build`时候没有指定`-t`,你的这个镜像就会变成`nginx:latest`，你之前的`nginx:latest`就会变成`nginx:<none>`,所以构建的时候尽量指定`-t`.

* 删除镜像 `docker rmi `,后面可以跟`镜像名:TAG`的格式，也可以用`IMAGES ID`.

* 运行容器 `docker run`

	我们看到很多时候需要交互模式，其中`-it`是交互模式的参数

	如果我们需要后台运行，参数`-d`

	`--name <容器名>`如果不使用这条参数，docker将会自己分配容器名

* 停止容器`docker stop`，删除容器`docker rm`
	
	查看在运行的容器`docker ps`

	查看所有的容器`docker ps -aq`
	
	查看所有新创建的容器`docker ps -l`
	 

有时候我们需要一下子清理掉全部运行中的容器:

```shell
$ docker stop $(docker ps -q)
```

然后删除掉全部容器：

```shell
$ docker stop $(docker ps -aq)
```

一条命令实现：

```shell
$ docker stop $(docker ps -q) & docker rm $(docker ps -aq)
```













放一张图，docker command，（图出自[原图链接](http://seanlook.com/2014/10/31/docker-command-best-use-1/)）

![docker command](http://ouonrxq3b.bkt.clouddn.com/docker_cli_stage.png)
