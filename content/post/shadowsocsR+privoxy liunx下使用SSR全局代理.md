---
title: shadowsocsR+privoxy liunx下使用SSR全局代理
date: 2017-10-18 09:32:05
tags:
    - 生活感想
---


## 前言

因为这段时间要去学校机房做实验，然后学校机房的网络可能经过了过滤，大陆外的ip一律无法访问，我们在机房用的系统是Ubuntu17.04，我直接用手机usb直接分享网络给机器使用（本来我还以为要使用复杂的网络设置，没想到现在的系统这么高级直接插上就可以使用了）。。但是我们还需要在liunx系统上进行外网访问，然后我测试了很多方法，比如以前的ss-Qt5，electron-ssr之类的GUI软件，但是都不行，后来有dalao指点，使用shadowsocksR-libv或者shadowsocksR-Python版加privoxy进行全局代理，然后我想了下，就试试吧，折腾了一晚终于完成了。

> 最后的shadowsocksR-libv的版本是2.6.3

## 配置

因为破娃妹子已经把源项目全部rm了，我们需要去shadowsocksR-backup里找到备份[shadowsocksr-libev](https://github.com/shadowsocksr-backup/shadowsocksr-libev)，然后`git clone`或者直接下载回到本地。

我们可以来到文档处，因为我们是ubuntu系统我就进入ubuntu的配置

输入命令

```shell
$ cd shadowsocks-libev
$ sudo apt-get install --no-install-recommends build-essential autoconf libtool libssl-dev \
    gawk debhelper dh-systemd init-system-helpers pkg-config asciidoc xmlto apg libpcre3-dev
$ dpkg-buildpackage -b -us -uc -i
$ cd ..
$ sudo dpkg -i shadowsocks-libev*.deb
```

最后的`shadowsocks-libev*.deb`是最后打包好的deb包，输入打包出来的文件名。

然后终极大坑来了

我们使用命令`ss-local`使用代理。

```shell
$ ss-local -o [obfs混淆协议] -O [protocol协议] -g [obfsparam混淆参数] -s [服务器地址] -p [服务器端口] -l [本地监听端口] -b [本地监听地址] -k [SS密码] -m [加密方式]
```

记住所有选项的顺序一定要按照以上顺序输入

> 有个巨坑，我在测试的时候发现重启一次电脑之后，ss-local就没有混淆协议的选项了！！！！如果你使用-O、-g,ss-local会报错没有该参数，后来我转移到另外一台ubuntu虚拟机又可以使用了，坑死。。。。推荐如果出现错误，直接把shadowsocksR-libev删除掉，重新安装包。

然后我们安装`privoxy`

```shell
$ sudo apt-get install privoxy
```

安装完成之后，我们进入`/etc/orivoxy`更改`config`，把这句注释掉：

```shell
# listen-address localhost:8118
```

然后在最后一行加上：

```shell
forward-socks5 / 127.0.0.1:1080 .
listen-address 127.0.0.1:8118
```

记住第一行后面有一个`.`，我配置的时候没有记得加`.`，然后一直不行吓死我了。

然后我们配置全局的http proxy, `sudo vim /etc/environment`，加入下面的代码

```shell
export http_proxy=http://127.0.0.1:8118
export https_proxy=http://127.0.0.1:8118
```

然后我们重启计算机，重新来一次`ss-local`连上代理，使用`curl www.google.com`试试

大功告成。