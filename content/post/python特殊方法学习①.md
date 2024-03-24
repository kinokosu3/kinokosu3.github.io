---
title: python特殊方法学习①
date: 2017-10-18 11:34:39
categories: 学习纪录
tags:
    - python
---

## 特殊方法使用

### __len__的使用

比如这样:

```python
class FrenchDeck(object):
	def __init__():
		self.__card = [1,2,3]
	
	def __len__(self):
		return len(self.__card)

>>> deck = FrenchDeck()
>>> len(deck)
3
```

<!-- more -->

直接获取长度，不用做过多的解释。

## 列表推导和生成器表达式

### 列表推导

例子：

```python
>>> sym = '$#%&*'
>>> codes=[]
>>> codes = [ord(symbol) for symbol in sym]
>>> codes
[36, 35, 37, 38, 42]
```

一个标准的列表推导，我们不必担心换行的问题，我们甚至可以这样使用：

```python
>>> codes = [ord(symbol, id) for symbol in sym
			     for id in range(0,5)]
```

### 生成器推导

这个和列表推导式的格式很像，仅仅把方括号换成圆括号。

生成器推导可以逐个产生元素，而不是直接产生一个完整的列表。

我们使用生成器表达式计算笛卡尔积

例子：

```python
>>> for tshirt in ('%s %s' % (c,s) for c in colors for s in sizes):
...     print(tshirt)
...
black S
black M
black L
white S
white M
white L
```

### 总结

列表生成式是直接生成一个完整的列表，而生成器推导则是可以逐个产生元素

一个例子：

```python
>>> for o in (ord(symbol) for symbol in sym):
...     print(o)
...
36
35
37
38
42
>>> [ord(symbol) for symbol in sym]
[36, 35, 37, 38, 42]
>>> type([ord(symbol) for symbol in sym])
<class 'list'>
>>> type(ord(symbol) for symbol in sym)
<class 'generator'>
```



