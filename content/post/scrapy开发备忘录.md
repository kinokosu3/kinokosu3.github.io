---
title: scrapy开发备忘录
date: 2017-11-10 22:16:08
categories: 备忘录
tags:
    - scrapy
---
## 前言

最近进了一个数据挖掘的小组，又是干爬虫的事，以前使用bs4+requests用的很顺，但是要优化的东西太多了，就打算学习个框架什么的，就看中了scrapy，希望它不错
<!-- more -->
---

用了一段时间之后，只能感叹，还是轮子好用。

## scrapy基础开发

### 一个简单的项目

基础项目格式

```
.
├── __init__.py
├── items.py
├── middlewares.py
├── pipelines.py
├── run.py
├── settings.py
└── spiders
    ├── __init__.py
    ├── main_spider.py
    ├── my_spider.py
    ├── quotes_spider.py
    └── test_spider.py

```

`spiders`文件夹里存放爬虫文件，`settings`是配置文件，`items`是字段文件，`run`是从文件执行爬虫，`middlewares`是中间件，`piplines`用来清理`items`或者存进数据库。

### 一个简单的爬虫文件

我们的文件放在spiders文件夹里，一个简单的爬虫：

`new_spiders.py`:

```python
import scrapy

class TestSpider(scrapy.Spider):
	name = "test"
    start_urls = [
        'https://www.bilibili.com/'
    ]

    def parse(self, response):
    	yield {"title": h3}

```

`name`是爬虫的名字，`start_urls`是爬虫开始爬的第一个页面。一个爬虫一个爬虫类。

`parse`方法是固定的，用来返回数据。

### scrapy shell调试

我们使用命令`scrapy shell url`，就可以进入这个url下的一个控制台

一般我们使用返回的`response`进行当前网站的分析，使用`response.xpath`就可以定位元素。

我们能做的：

```
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x10068c320>
[s]   item       {}
[s]   request    <GET https://www.bilibili.com/>
[s]   response   <200 https://www.bilibili.com/>
[s]   settings   <scrapy.settings.Settings object at 0x10754eac8>
[s]   spider     <DefaultSpider 'default' at 0x1078eeda0>
[s] Useful shortcuts:
[s]   fetch(url[, redirect=True]) Fetch URL and update local objects (by default, redirects are followed)
[s]   fetch(req)                  Fetch a scrapy.Request and update local objects 
[s]   shelp()           Shell help (print this help)
[s]   view(response)    View response in a browser
```

### 选择器(Selectors)

我们通常使用`xpath`来解析网页。比如：

```python
response.xpath('//title/text()').extract()
```

我们调用`extract()`方法来获取原文数据，整句返回一个`List`。

我们还能使用正则表达式，比如：

```python
response.xpath('//title/text()').re('\d+')
```

不需要加`extract()`方法。

### Items

`scrapy`的字段设置在项目里的`items.py`文件里设置。

```python
import scrapy


class FirstSpiderItem(scrapy.Item):
    # define the fields for your item here like:
    title = scrapy.Field()
    pass

```

设置很简单，所有的字段都设置成`Field`。

使用Items也很简单，我们把字段导入：

```python
from ..items import DoubanBookStatusItem
```

在`parse`方法（也可以是自己定义的返回数据方法）里可以这样使用：

```python
 item = DoubanBookStatusItem()
 item['press'] = response.xpath('//*[@id="info"]').re('(?<=出版社:</span> ).*?(?=<br>)')[0]    # 出版社
        
```

`press`为我们定义的字段名。

> 有一个需要注意的，我们使用迭代器yield返回的数据全部都是字典格式的。

### piplines

> 功能需要在settings里面设置

我们使用mongodb保存,直接贴出代码


```python
class MongoPipline(object):
    collection_name = 'scrapy_items'	# mongodb集合名

    def __init__(self):
        self.mongo_server = 'localhost'
        self.mongo_db = 'runoob'
        self.port = 27017
        self.client = None
        self.db = None

    def open_spider(self, spider):
        
        self.client = pymongo.MongoClient(self.mongo_server, self.port)
        self.db = self.client[self.mongo_db]

    def close_spider(self, spider):
        self.client.close()

    def process_item(self, item, spider):
        self.db[self.collection_name].insert_one(dict(item))
        return item

```

`piplines`大概能实现下面功能：

定义一个Python类，然后实现方法`process_item(self, item, spider)`即可，返回一个字典或`Item`，或者抛出`DropItem`异常丢弃这个`Item`。

或者还可以实现下面几个方法：

* `open_spider(self, spider)` 蜘蛛打开的时执行
* `close_spider(self, spider)` 蜘蛛关闭时执行
* `from_crawler(cls, crawler)` 可访问核心组件比如配置和信号，并注册钩子函数到Scrapy中。





