---
title: Vue从零养成
date: 2018-04-09 10:47:39
tags:
    - Vue
    - web开发
---

## 前言

现在前端框架的乱战从三强分立(React,Vue,AngularJS)逐渐变成了两强(React,Vue),虽然一直在做着数据分析的工作，但是还是想把一些东西直接放在web上可视化或者交互，趁着有一个清明假期就去学习了一下`Vue`框架(React接触过之后感觉有些难懂)，以下是一些备忘录,细节不会说的太多，主要实现细节可以baidu or Google

现代前端较为复杂，有了webpack之后，更像是软件开发了(模块化带来的错觉)

ES6/ES5+了解一下

> 使用的版本：
> - Vue 2.9.3
> - npm 5.6.0
> - Vue-router 3.0.1
> - webpack 3.11.0

命令行的操作使用windows10下的console emulator(所以所有的命令都通配Liunx，但是要注意路径)
npm国内安装会稍微慢，如果嫌慢可以科学上网
<!-- more -->
### Webpack了解

[webpack中文文档](http://zhaoda.net/webpack-handbook/module-system.html)

### scss了解

[scss中文网](https://www.sass.hk/)

## Vue-cli的基础使用

首先，对web开发三巨头(HTML,CSS,JavaScript)还不熟练的，可以单网页练习使用`Vue`,但是我们可以使用官方推荐的脚手架，脚手架使用`webpack`可以直接打包，官方帮助我们设定了`webpack`的打包文件，免去我们编写`webpack`配置的痛苦，我们可以专心开发`Vue`的组件。

对于`npm`的本地和全局安装，反正我喜欢本地安装(python virtualenv的后遗症)，我们安装`Vue-Cli`：

```shell
npm install vue-cli --save-dev
```

我们初始化一个`webpack`模板:

```shell
./node_modules/.bin/vue init webpack my-project
```

`my-project`是我们项目的名字

接下来会有一大堆设置的东西，如果看不懂直接一路回车跳过

进入我们的项目

```shell
cd my-project
```

项目目录:{%asset_img 1.png dir%}

然后跑一下，启动命令`npm start`

> npm 命令的自定义可以在package.json里面的`scripts`键值看到，默认的命令重写是:
> "scripts": {
>    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
>    "start": "npm run dev",
>    "unit": "jest --config test/unit/jest.conf.js --coverage",
>    "e2e": "node test/e2e/runner.js",
>    "test": "npm run unit && npm run e2e",
>    "build": "node build/build.js"
>  },
> 里面我们可以很OK的看到，其实npm start是等价于npm run dev的。

我们启动的这个命令是`热重载`，是`webpack`的一个非常牛逼的功能，他会启动一个简单的server，你每次改代码它都会实时显示出来。主要细节可以baidu or Google

命令启动完成后，我们得到一个本地服务器地址，访问它:

{% asset_img 2.png server_url%}

得到hello world界面:

{% asset_img 3.png hello world %}

简单的`vue-cli`就搭建好了