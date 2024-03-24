---
title: python字符串技巧
date: 2017-08-16 13:42:55
categories: 学习心得
tags:
    - python
---
***

### python判断字符串是否包含子字符串的方法

#### 方法①：`in`方法

```python
@logging
site = "www.baidu.com"
if "baidu" in site:
	print('site contains baidu')
```

输出'site contains baidu'.


#### 方法②：字符串`find`函数

```python
s = "www.baidu.com"
if s.find("baidu") == -1:
	print("no")
else:
	print("have")
```

这个find函数是搜索子字符串是否在母字符串中，不在返回-1，在则返回索引值
