---
title: Docker学习心得①
date: 2017-08-10 19:02:45
tags:
    - docker
---
***

### docker中RUN、CMD、ENTRTPOINT的区别

一开始我是对这三个理解的不太够透彻，因为这三个都是运行shell下的命令

最重要的是他们的**运行时机**不一样，RUN是bulid镜像是用到的指令，最终会commit到镜像

CMD和ENTRTPOINT则是运行时候使用，运行镜像时就会运行这些命令



