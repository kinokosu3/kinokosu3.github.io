---
title: Docker学习纪录②
date: 2017-08-16 11:42:55
categories: 学习纪录
tags:
    - docker
---
***

### Dockerfile定制镜像

> 大量引用Docker--从入门到实践的内容

Gitbook地址：[Docker--从入门到实践](https://www.gitbook.com/book/yeasy/docker_practice/details)

### FROM指定基础镜像

```docker
FROM nginx
```

指定一个`nginx`基础镜像

### RUN执行命令

- shell格式
- exec格式

<!--more-->
dockerfile一条指令建立一层，`RUN`指令应该这么写

```docker
FROM debian:jessie

RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```

而不是一行一个`RUN`

### 镜像上下文（context）

如果注意，会看到 `docker build` 命令最后有一个 `.`。`. `表示当前目录

为什么呢？？

理解`docker bulid`的工作原理，我们所用的docker命令是客户端工具，表面上我们是使用docker命令来执行各种docker功能，但是实际上所有的远程调用形式都是在服务端（docker引擎）完成。C/S设计

如何才能让服务端获得本地文件呢？？

这就引入了上下文的概念。当构建的时候，用户会指定构建镜像上下文的路径，docker build 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。

比如

```docker
COPY ./package.json /app/
```

这并不是要复制执行 `docker build` 命令所在的目录下的 package.json，也不是复制 Dockerfile 所在目录下的 package.json，而是复制 上下文（context） 目录下的 package.json

### Dockerfile 指令

### COPY 复制文件

格式

- COPY <源路径>... <目标路径>
- COPY ["<源路径1>",... "<目标路径>"]

`COPY` 指令将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置。比如：

```docker
COPY package.json /usr/src/app/
```

### ADD 更高级的复制文件

格式和`COPY`差不多

这个只适合自解压的场合

如果 <源路径> 为一个 `tar` 压缩文件的话，压缩格式为 `gzip`, `bzip2` 以及 `xz` 的情况下，`ADD` 指令将会自动解压缩这个压缩文件到 <目标路径> 去。

```docker
FROM scratch
ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /
...
```

这样就会自动解压到根目录

### CMD容器启动命令

`CMD` 指令就是用于指定默认的容器主进程的启动命令的。

在运行时可以指定新的命令来替代镜像设置中的这个默认命令，比如，`ubuntu` 镜像默认的 `CMD` 是 `/bin/shell`，如果我们直接 `docker run -it ubuntu` 的话，会直接进入 `shell`。我们也可以在运行时指定运行别的命令，如 `docker run -it ubuntu cat /etc/os-release`。这就是用 `cat /etc/os-release` 命令替换了默认的 `/bin/shell` 命令了，输出了系统版本信息。

Docker容器不是虚拟机，所有的应用都应该前台运行

容器内没有后台的概念

> CMD、RUN、ENTRYPOINT概念理解可以看docker心得①

`shell`模式并不好用。推荐使用`exec`模式

### ENTRYPOINT入口

#### 可以把镜像当命令使用

跟在镜像后的是command，会替换CMD的默认值（写在dockerfile里面的cmd值）

如果都是使用`CMD`，我们在运行需要带参数的镜像就不行了会报错

```docker
CMD ["curl", "-s", "http://ip.gs"]
```

这样就无法就后面接上参数

报错，因为`-i`不是一个CMD命令

```shell 
$ docker run ip -i
```

如果是使用`ENTRYPOINT`，CMD的内容会将会作为参数传给`ENTRYPOINT`，比如

```shell
$ docker run ip -i
```
`-i`将会作为新的CMD传回来

#### 应用运行前的准备工作

启动了容器就是启动主进程，但是我们在这之前需要准备一下其他的工作

可以写一个脚本判断CMD后面的命令来选择执行某些工作

比如启动一个redis容器的时候，如果不是redis-server命令启动，我们就会使用root用户登录

```shell
$ docker run -it redis id
uid=0(root) gid =0(root) groups =0 (root)
```


###未完待续~












