---
title: python装饰器
date: 2017-10-25 10:01:14
categories: 学习纪录
tags:
    - python
---

## 装饰器基础

### 函数即为对象

python中的一切都是对象，对象有什么特征呢？比如：

- 你可以把它赋值给一个变量
- 你可以赋值它
- 你可以给它添加属性
- 你可以作为函数参数来传递它

<!-- more -->

```python
>>> print(ObjectCreator) # 你可以打印一个类,因为它是一个对象
<class '__main__.ObjectCreator'>
>>> def echo(o):
...       print(o)
...
>>> echo(ObjectCreator) # 你可以把类作为参数传递
<class '__main__.ObjectCreator'>
>>> print(hasattr(ObjectCreator, 'new_attribute'))
False
>>> ObjectCreator.new_attribute = 'foo' # 可以给一个类添加属性
>>> print(hasattr(ObjectCreator, 'new_attribute'))
True
>>> print(ObjectCreator.new_attribute)
foo
>>> ObjectCreatorMirror = ObjectCreator # 可以把类赋值给一个变量
>>> print(ObjectCreatorMirror.new_attribute)
foo
>>> print(ObjectCreatorMirror())
<__main__.ObjectCreator object at 0x8997b4c>
```

函数还能在别的函数里定义，函数还能返回另外一个函数。

## 实现装饰器

### 实现一个最简单的装饰器

一个小例子：

```python
# 其他函数作为参数
def my_decorator(func):
	def the_wrapper_fuction():
		# 在原始函数被调用前的代码放在这里
		print("123")
		func()
	return the_wrapper_fuction

@my_decorator
def func():
	print("main")

>>> 123
>>> main
```

### 装饰器高级用法










