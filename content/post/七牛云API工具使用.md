---
title: 七牛云API工具使用
date: 2017-09-06 16:41:54
tags:
    - 七牛云
categories: 文档纪录
---

## qshell的使用

### qshell安装

在这里下载

[qshell下载链接](https://developer.qiniu.com/kodo/tools/1302/qshell#7)

选择自己平台的文件下载：


如果在使用的时候，报错`Permission Denied`,可以使用命令`chmod +x qshell`添加执行权限。
如果是liunx或者osx用户，可以添加PATH来直接启动
### 添加或者显示当前用户的AccessKey和SecretKey

秘钥可以在个人中心查看

命令格式`qshell account [AccessKey] [SecretKey]`

### 上传文件

命令格式 `qshell fput [空间名] [空间文件名] [文件路径]`