---
title: tensorflow基础备忘录
date: 2018-04-23 22:31:32
tags:
    - tensorflow
---
## 基础概念

### 计算图

`tensorflow`上的每一个计算都是计算图上的一个节点，节点之间的边描述了计算之间的依赖关系，`TensorFlow`会默认维护一个计算图。在`tensorboard`中可以看到可视化的计算图。
<!-- more -->
### 张量
定义一个张量:`a = tf.constant([1.0, 2.0], name='a')`，`name`是这个张量的名字，张量几乎就是多维数组，张量有3个属性，名字(name)、维度(shape)、类型(type)。张量本身不存储数字。

常量 tf.constant()
变量 tf.Variable()
占据位置的常量(好像是) tf.placeholder()
> 使用时候sess.run(feed_dict={}),一个变量一个键值


### 会话
使用上下文来管理会话的关闭和开启，`with tf.Session() as sess: sess.run()`


## TensorFlow方法

### 张量方法

.get_shape 获取维度信息

### 数学方法

tf.matmul(x, y) x,y矩阵相乘

### 随机数生成方法

tf.random_normal 正太分布
tf.truncated_normal 正态分布，随机出来的值偏离平均值超过2个标准差，这个数会随机
tf.random_uniform 平均分布
tf.random_gamma Gamma分布

### 常数生成方法

tf.zeros 产生0的数组 tf.zeros([2,3]) 产生一个[[0,0,0], [0,0,0]]
tf.ones  产生1的数组 
tf.fill 产生一个全部为给定数字的数组 tf.fill([2,3], 9)
tf.constant 产生一个常量

### 初始化所有变量
init_op = tf.global_variables_initializer()
sess.run(init_op)

