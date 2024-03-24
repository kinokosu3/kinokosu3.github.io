---
title: win下的Clion配置
date: 2017-10-10 18:55:41
tags:
    - Clion
---

### 运行环境配置

我们需要`mingw`和`Cmake`，这个是clion需要的运行环境

#### mingw安装

百度mingw，然后点击download/installer,就会自动跳转到下载页面，然后安装，点击之后，一路continue，有个安装路径自己选择一下，如果选择其他路径，可以先创建一个空文件夹再选择其安装。

然后会出现下载安装器的界面，安装完成之后继续按continue退出。
![mingw](http://ouonrxq3b.bkt.clouddn.com/Clion-config/mingw_2.png)

然后会跳转到这个界面
![mingw](http://ouonrxq3b.bkt.clouddn.com/Clion-config/mingw_3.png)

我们在要安装的选项右键之后点击`Make for Intallation`，然后选择以下的选项:
![mingw](http://ouonrxq3b.bkt.clouddn.com/Clion-config/mingw_4.png)

然后安装，在左上的`Installation`选项卡里选择，`Apply Changes`,就可以安装成功了

#### Cmake安装

我们在官网找到安装页面[Download](https://cmake.org/download/)

找到cmake 3.8.2的下载的地方，(其他的版本clion不认我也不知道为什么)，选择适合自己系统的版本下载，我们是windows，就选择windows的下载好了。

然后一路安装就OK了