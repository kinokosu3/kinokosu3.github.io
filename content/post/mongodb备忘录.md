---
title: mongodb备忘录
date: 2017-08-25 19:14:07
tags:
    - mongodb
---
***

### 在monogo shell的命令使用

#### 连接数据库

本地数据库链接，默认的路径是`/data/db/`,可以使用`--dbpath`来指定数据库路径

```shell
$ sudo mongod --path ~/Desktop/db/
```

<The rest of contents | 余下全文>
#### 创建数据库

语法：`use DATABASE_NAME`

也有切换数据库操作的意思

```shell
> use runoob
switched to db runoob
> db
runoob
```
#### 查看全部数据库

```shell
> show dbs
dmin   0.000GB
local   0.000GB
runoob  0.000GB
```

#### 删除数据库

语法：`db.dropDatabase()`

```shell
> db.dropDatabase()
{ "dropped" : "runoob", "ok" : 1 }
```

#### 创建集合

语法：`db.createCollection(name,option)`

`options`参数，类型是`Document`,可选有字段`capped`, `autoIndexId`,`size`,`max`

```shell
> db.createCollection("firstcollection")

```

带有option参数的：

```shell
> db.createCollection("firstcollection",{capped:ture})
```

一般情况下不用主动创建集合，插入文档的参数中有`collection_name`

#### 删除数据库中的一个集合

语法：`db.collection.drop()`

比如切换到runoob数据库删除里面的一个site集合

```shell
> use runoob
switched to db runoob
> show tables
site
> db.site.drop()
true
> show tables
> 
```

#### 插入文档

Mongodb文档的数据结构和json基本一样，所有的数据都是BSON(存储在集合中的)

语法：`db.COLLECTION_NAME.insert(document)`

#### 更新文档

有`update()`和`save()`方法来更新集合中的文档

语法格式如下：

```shell
> db.collection.update(
	<query>,
	<update>,
	{
		upsert:<boolean>,
		multi:<boolean>,
		writeConcern:<document>
	}

)
```

参数说明：
* `query` : `update`的查询条件，类似`sql update`查询内`where`后面的。

* `update` : `update`的对象和一些更新的操作符（如$,$inc...）等，也可以理解为`sql update`查询内`set`后面的

* `upsert` : 可选，这个参数的意思是，如果不存在`update`的记录，是否插入`objNew`,`true`为插入，默认是`false`，不插入。

* `multi` : 可选，mongodb 默认是`false`,只更新找到的第一条记录，如果这个参数为`true`,就把按条件查出来多条记录全部更新。

比如我们在runoob插入文档

```shell
> db.runoob.insert({title:'MongiDB',description:"Nosql"})
```

然后我们通过`update()`来更新标题

```shell
> db.runoob.update({'title':'MongoDB'},{$set:{'title':'Mongo'}})
```

以上的语句只会更新找到的第一个文档，如果你要更新多个同样的文档，你需要把`multi`参数打开

```shell
> db.runoob.update({'title':'MongoDB'},{$set:{'title':'Mongo'},{multi:ture}})
```

接下来我们使用`save()`来更新文档，`save()`方法通过传递的文档来替换已有文档。

语法格式如下：

```shell
db.collection.save(
	<document>,
	{
	writeConcern:<document>
}
)
```

比如我们替换了_id为599ea3130a0004f919342ea5的文档数据

```shell
> db.runoob.save({"_id":ObjectId("599ea3130a0004f919342ea5"), "name":"kinoko3"})
```

### MongoEngine使用


#### 定义一个文档

```python
from mongoengine import *  

import datetime


class Page(Document):  

    title = StringField(max_length=200, required=True)  

    date_modified = DateTimeField(default=datetime.datetime.now)

```

#### 保存到数据库

```python
if __name__ == '__main__':
	# 第一种保存方法
	page = Page()
	page.title = "Hatsune Miku"
	page.save()
	# 第二种保存方法
	page_1 = Page(title="Hatsune Miku")
	page_1.save()
```

保存之后mongoenine将会创建一个新的以`class`名命名的集合，比如`class Page(Document)`的一个类，创建的集合将会是`page`,查看创建的集合可以在`mongodb shell`里使用命令`show tables`或者`show collections`查看

#### 动态文档（DynamicDocument）

动态文档可以在保存数据的时候给数据自定不同的字段
比如：

```python
class Page(DynamicDocument):
	title = StringField(max_length=200)

if __name__ == '__main__':
	page = Page(title='Hatsune Miku')
	page.tags = ['mongodb', 'mongoengine']
	page.save()
```

`page.tags`就是重新在这个文档里插入一个新的字段数据，在保存的时候，这个字段是之前没有在文档类里声明的，如果你不继承动态文档，直接在保存的时候添加字段，运行的时候不会报错，但是新加入的字段不会保存.上面的文档在`mongodb shell`中显示：

```shell
> {
        "_id" : ObjectId("59a2b15c6f1d6513f3401668"),
        "name" : "Hatsune Miku",
        "tags" : [
                "mongodb",
                "mongoengine"
        ]
}
> 
```

`tags`字段是我们后来添加的。













