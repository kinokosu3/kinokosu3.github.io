---
title: liunx全养成①
date: 2018-03-11 11:37:21
tags:
    - liunx
    - Ubuntu
categories: 备忘录
---
## 前言

最近小组那边找了一大批新人，看起来都是大佬的样子(笑，而且我也要去上课之类的，所有只好重新开一台阿里云的服务器，上个学期那台已经被阿里云释放了，但是我依旧做好了备份，所以应该没有数据丢失(上个学期爬的100M豆瓣数据说丢就丢毫不含糊)，既然重新来，就做一个记录，当一个wiki使用。该文会长期更新。。
<!-- more -->
## 服务器前期工作

因为安全问题和方便的问题，安全登录我们先做好准备，首先做好`ssh`的登录准备。

### 生成ssh-key实现无密码登录

无论是本地还是远程，生成`ssh`秘钥都是一句命令:

```shell
ssh-keygen -t rsa -C "your email"
```

实现免密码登录只需要把本地`.ssh/id_rsa.pub`内的秘钥放在远程服务器的`.ssh/authorized_keys`内即可实现无密码登录。

### ssh禁止密码登录

```shell
vim /etc/ssh/sshd_config
```

禁用密码验证
PasswordAuthentication no  //修改为no  133行（最后一行）

## 更新安装列表

`apt-get update`

## 布置python环境

阿里云的Ubuntu系统自带两种稳定版`python`，一种是`Python 2.7.12`，一种是`Python 3.5.2`，但是我都不想用，我就是想用又新又稳定的，比如`Python-3.6.4`。

我们在python官网上下载编译包`wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tgz`

然后解压`tar -zxvf Python-3.6.4.tgz`。

> 我们首先先去编译安装一个zlib，不然安装pip和setuptool会出错
> 我们还需要安装openssl,libssl-dev, libsqlite3-dev
> 然后修改python-3.6.4/Modules/Setup，修改
```shell
# Socket module helper for socket(2)  
_socket socketmodule.c 
  
# Socket module helper for SSL support; you must comment out the other  
# socket line above, and possibly edit the SSL variable:  
#SSL=/usr/local/ssl  
_ssl _ssl.c \  
-DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \  
-L$(SSL)/lib -lssl -lcrypto 

```

！！！！！！！！！！！！！一定要完成上面的注意事项再进行编译安装！！！！！！！！！！！！！！！

我们进入刚刚解压出来的文件夹里，执行3连:

```shell
./configure
make
sudo make install
```

### python虚拟环境virtualenv

`pip3 install virtualenv`

查看`Python3.6`安装路径:`which pyhton3.6`

创建虚拟环境: `virtualenv -p /usr/bin/python3 py3env`

进入虚拟环境: `source py3env/bin/activate`

退出虚拟环境： `deactivate`

## 布置ipython-notebook

`pip install ipython`、`pip install jupyter`.

启动时候注意端口和地址。



