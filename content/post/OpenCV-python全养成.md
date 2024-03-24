---
title: OpenCV-python全养成
date: 2018-03-22 21:38:48
tags:
    - Python
    - OpenCV
---
## 前言

中文的opencv-python内容太少了！！！！没办法只好啃英文文档了,官方文档saikou！！！

地址[OpenCV-Python Tutorials](https://docs.opencv.org/3.0-beta/doc/py_tutorials/py_tutorials.html)

<!--more-->

## 入门

### 基本操作

`cv2.imread（），cv2.imshow（），cv2.imwrite（）`

`cv2.imread()`,可以使用以下3个flag:

* `cv2.IMREAD_COLOR`：加载彩色图像。图像的任何透明度都将被忽略。这是默认标志。
* `cv2.IMREAD_GRAYSCALE`：以灰度模式加载图像
* `cv2.IMREAD_UNCHANGED`：加载包含Alpha通道的图像

代号是1,0,-1

`img = cv2.imread('1.jpg', 0)`

`cv2.imshow()`

* `cv2.waitKey（）`是一个键盘绑定函数，如果传递0，他将无限期等待键盘输入，敲击后，程序会继续，也可以使用特定的按键。

* `cv2.destroyAllWindows（）`只是销毁我们创建的所有窗口。
* 如果你想销毁任何特定的窗口，使用函数`cv2.destroyWindow（）`作为参数传递确切的窗口名称.
* 如果你先创建了窗口，再创建图片，可以使用`cv2.namedWindow()`,有两个`flag`,默认`cv2.WINDOW_AUTOSIZE`,`cv2.WINDOW_NORMAL`可以调整窗口大小。

`cv2.imwrite()` 保存一张图片 `cv2.imwrite('messigray.png',img)`

### Matplotlib使用

一个简单的例子:

```python
import numpy as np
import cv2
from matplotlib import pyplot as plt

img = cv2.imread('messi5.jpg',0)

plt.imshow(img, cmap = 'gray', interpolation = 'bicubic')
plt.xticks([]), plt.yticks([])  # to hide tick values on X and Y axis
plt.show()

```

`matplotlib.pylot.imshow`选项:

cmap [colormap-example](https://matplotlib.org/gallery/color/colormap_reference.html#sphx-glr-gallery-color-colormap-reference-py)

* perceptual uniform sequential colormaps：感知均匀的序列化 colormap
* sequential colormaps：序列化（连续化）色图 colormap； 
* * gray：0-255 级灰度，0：黑色，1：白色，黑底白字；
* * gray_r：翻转 gray 的显示，如果 gray 将图像显示为黑底白字，gray_r 会将其显示为白底黑字；
* diverging colormaps：两端发散的色图 colormaps； 
* * seismic
* qualitative colormaps：量化（离散化）色图；
* miscellaneous colormaps：其他色图；

> 其实说白了就是一个色彩参照

interpolation 内插法选择

有很多选择，比如:

* nearest 最近邻内插
* bilinear 双线性内插
* bicubic 双三次内插

## 视频使用

暂不了解

## 绘制函数

开始之前我们先讲一个问题

### 图像通道问题

> 较难理解，知乎有一个同理问题[如何通俗易懂地讲解 Photoshop 中的「通道」概念？](https://www.zhihu.com/question/21849710)这个通俗易懂，这个就是彩色图像模型，下面的解释来自[OpenCV学习笔记（7）图像的通道（channels）问题](https://blog.csdn.net/chenyusiyuan/article/details/4662783)

图像的通道只有1,3,4通道，因为我们描述一个像素点，如果是灰度，我们一个通道就可以了，如果一个像素点，有RGB三种颜色来描述它，就是三通道.4通道大概是加一个A通道(RGBA)，透明度，又叫alpha通道.

### 线段

传递起始和结束坐标、颜色、厚度
比如蓝色的厚度5px的线段
`cv2.line(img,(0,0),(511,511),(255,0,0),5)`

### 矩形

左上角和右下角的坐标
`cv2.rectangle(img,(384,0),(510,128),(0,255,0),3)`

### 圆

中心坐标和半径
`cv2.circle(img,(447,63), 63, (0,0,255), -1)`

### 多边形

顶点坐标，需要int32类型，将其转换成rowsx1x2的形状的数组，rows为顶点数

```python
pts = np.array([[10,5],[20,30],[70,20],[50,10]], np.int32)
# newshape重置参数 行列每个
pts = pts.reshape((-1,1,2))
cv2.polylines(img,[pts],True,(0,255,255))
```

> cv2.polylines用于绘制多组线，line只能绘制一组

