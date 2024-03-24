---
title: python知识备忘录
date: 2017-08-18 09:24:28
categories: 备忘录
tags:
    - python
---
***

#### 生成requirement文件

在当前目录生成`requirements`文件

```shell
$ pip freeze > requirements.txt
```

从`requirements`安装库

```shell
$ pip install -r requirements.txt
```

pip安装时有一个不保留缓存的参数`--no-cache-dir`

#### 找到site-packages文件夹

因为我用brew安装的python3，所以那个路径深似海啊，里面存放了我们的pip安装的一些第三方库

我们可以在python交互模式里面输入

```python
import site; site.getsitepackages()
```
就出来路径了





