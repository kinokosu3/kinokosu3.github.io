---
title: mongodb一些部署设置
date: 2017-11-19 20:27:08
categories: 备忘录
tags:
    - mongodb
---

## 前言

最近也要布置一个爬虫项目到云端，之前一直都在本地测试，这次部署也暴露了很多问题，就记一下。
<!-- more -->
## mongodb部署

### mongodb后台运行

我们可以使用配置文件进行运行，带参数`-f`。后台运行可以在配置文件里设置`fork=true`。

下面贴出简单的配置文件：

```shell
dbpath=/data/db/  # 数据库路径

logpath=/data/db/mongodb.conf  #  输出日志路径

logappend=true		# 日志添加

bind_ip = 0.0.0.0   # 设置可以外网访问

port = 27017      # mongodb默认端口

journal=true    

fork=true   # 后台运行
```

#### 关闭后台运行

我们可以使用命令`ps -aux | grep mongod`，找到mongod的PID，然后使用命令`kill -2 PID`安全关闭。

### mongodb警告解决

我们使用mongo shell链接mongod会发现有一些警告，比如这个：

```
2015-10-21T09:03:24.256+0800 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/enabled is 'always'.  
2015-10-21T09:03:24.256+0800 I CONTROL  [initandlisten] **        We suggest setting it to 'never'  
2015-10-21T09:03:24.256+0800 I CONTROL  [initandlisten]   
2015-10-21T09:03:24.256+0800 I CONTROL  [initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/defrag is 'always'.  
2015-10-21T09:03:24.256+0800 I CONTROL  [initandlisten] **        We suggest setting it to 'never'  

```

查了一下，好像对内存消耗很大的服务都不喜欢它。

我们有几种方法可以关闭，可以这样：

```shell 
echo never > /sys/kernel/mm/transparent_hugepage/enabled  
echo never> /sys/kernel/mm/transparent_hugepage/defrag  
```

还可以永久关闭它,修改/etc/rc.local：

```shell

if test -f /sys/kernel/mm/transparent_hugepage/enabled; then  
   echo never > /sys/kernel/mm/transparent_hugepage/enabled  
fi  
if test -f /sys/kernel/mm/transparent_hugepage/defrag; then  
   echo never > /sys/kernel/mm/transparent_hugepage/defrag  
fi 

```

还有一个问题一直不知道怎么解决，就是

```shell
2017-11-19T17:13:57.343+0800 I CONTROL  [initandlisten] ** WARNING: soft rlimits too low. rlimits set to 7857 processes, 65535 files. Number of processes should be at least 32767.5 : 0.5 times number of files.

```

明明修改了那个什么limits文件加了mongod 64000什么的都不行。

### mongodb用户权限管理

在mongo shell里命令有：

```shell

# 创建用户
> db.createUser({
	user:"<name>",
	pwd:"<password>",
	roles:[
		role: "<role>",
		db: "<database>"
	]
})

# 验证用户
> db.auth(<username>,<password>)

# 修改用户密码
> db.changeUserPassword(<username>, <password>)

# 删除用户
> db.dropUser(<username>, <password>)

# 删除全部用户
> db.dropAllUsers()

# 获取用户
> db.getUser(<username>)

# 获取多个用户
> db.getUsers()

# 更新用户资料
> db.revokeRolesFromUser()
> db.revokeRolesFromUser( "accountUser01",
                        [ { role: "read", db: "stock" }, "readWrite" ],
                        { w: "majority" }
                      )

```

root权限必须在admin数据库里验证。

## pymongo里用户验证

```python

client['runoob'].authenticate('scrapy','abc','runoob',mechanism='SCRAM-SHA-1')

```








