---
title: pandas一些技巧
date: 2018-06-06 16:38:26
tags:
    - python
    - pandas
---

## DataFrame的一些技巧操作

### 转置

方法加T `.T`

### 数据合并

主要方法 `pd.concat`, 参数有

* `axis` 合并方向 `axis=0` 纵向合并
* `ignore_index` 重置index ignore_index=True
* `join` 合并方式 `join='outer'`为预设值，因此未设定任何参数时，函数默认join='outer'。此方式是依照column来做纵向合并，有相同的column上下合并在一起，其他独自的column个自成列，原本没有值的位置皆以NaN填充。

### 遍历dataframe行

* 方法`df.iterrows()`, 迭代，使用`for index, row in df.iterrows()`, index返回index， row返回行

应该会有相同的方法遍历的列。

### 重置索引

可以使用`reindex`、或者重新对`pandas.DataFrame.index`进行赋值,如果仅仅是计数索引(比如从0开始)，可以使用语句`df1.reset_index(drop=True, inplace=True)`，`drop`是是否保存原索引列
，`inplace`是否直接对dataframe改写。




