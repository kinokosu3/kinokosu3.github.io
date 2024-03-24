---
title: React学习纪录
date: 2017-09-17 16:24:19
categories: 学习纪录
tags:
    - React
---
## 开始React的世界

### 配置React

首先我们要安装`Node.js`环境，然后有`npm`安装环境，然后我们在命令行里执行

```shell
$ npm install create-react-app
```

然后我们就会有一个`React`脚手架，可以迅速构建`React`开发环境

```shell
$ npm install -g create-react-app
$ create-react-app my-app
$ cd myapp/
$ npm start
```
因为`npm`在国内很慢。。推荐在第一次创建项目的时候挂个科学上网什么的

然后我们会看到我们的第一个demo

### Material-UI 使用教程

我们在当前项目的`package.json`中修改：

```json
"dependencies": {
    "material-ui": "^0.19.1",
    "react": "^15.6.1",
    "react-dom": "^15.6.1",
    "react-scripts": "1.0.13"
  },
 ```
 将`material-ui`添加进来,然后执行`npm update --scripts-prepend-node-path=auto`,将`material-ui`包添加进当前脚手架

然后我们开始构建第一个demo。







