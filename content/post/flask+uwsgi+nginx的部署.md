---
title: flask+uwsgi+nginx的部署
date: 2017-10-11 22:39:05
categories: 备忘录
tags:
    - flask
---

## 前言

哇，之前写一个`flask`项目的时候，一直打算部署，看了不少的教程，最后选择了`flask`+`uwsgi`+`nginx`的搭配，然后在Ubuntu server里尝试上线，但是就是不行，一直报错502，一直找不到问题所在，后来在家里的一台烂liunx笔记本尝试上线（一样是Ubuntu），这已经距离我的`flask`项目写好过了几个月了，然后一次就成功了，一脸蒙蔽，这个教程并没有太多的对于`uwsgi`和`nginx`的性能优化之类的，仅仅保留了最基本的配置。不会对配置做过多的解释，如果想知道配置文件的解释，可以自己谷歌或者百度查看`uwsgi`和`nginx`的配置文件解释。仅仅做一个备忘录。

## 配置

### nginx配置

nginx配置文件路径在`/etc/nginx`

nginx配置文件如下：


```json
server{
        listen 80;
        server_name localhost;
        charset utf-8;
        keepalive_timeout 0;
        client_max_body_size 75M;
        location / {
                include uwsgi_params;
                uwsgi_pass unix:/home/su/upload_file-master/demo.sock;

        }

}
```

`server_name`为服务器地址，这里因为是本地上线，所以填了本地的地址，这里一个很主要的和`uwsgi`交互的地方在与`uwsgi_pass`这个配置选项，链接`uwsgi`的socket地址，生成这个socket的路径会在`uwsgi`的配置文件里配置。可以看下面的`uwsgi`配置。

这个文件的名字是`nginx.conf`，尽量不要修改，我们将这个文件放在`/etc/nginx/site-enabled/`里，然后修改一下`/etc/nginx/nginx.conf`里的`include /etc/nginx/sites-enabled/*.conf`这一项。

![nginx.conf](http://ouonrxq3b.bkt.clouddn.com/flask-nginx-uwsgi/nginx-conf.png)

然后我们检查一下`nginx`的配置文件正确性，输入命令`sudo nginx -t`，如果显示:

![nginx-config](http://ouonrxq3b.bkt.clouddn.com/flask-nginx-uwsgi/nginx-test.png)

就表示`nginx`配置文件配置正确

### uwsgi 配置

建议将uwsgi配置文件放在项目根目录下，后缀为`.ini`

uwsgi配置

```json
[uwsgi]
processes = 8

wsgi-file = /home/su/upload_file-master/demo.py

socket = /home/su/upload_file-master/demo.sock

chmod-socket = 666
module = demo
callable = app
```

`wsgi.file`文件为`flask`的服务器入口，比如哪个文件有像这样的`app.run(debug=True, port=9090, host="0.0.0.0")`（其实就是服务器启动），`socket`的保存路径就是`nginx`配置文件中`uwsgi_pass`的路径，这个路径一定要对。`chomd-socket`把`socket`的权限改写成所有人可写可读。

> callable选项为flask中的`app = Flask(__name__)`



