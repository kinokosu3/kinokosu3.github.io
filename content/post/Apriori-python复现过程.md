---
title: Apriori-python复现过程
date: 2017-12-10 10:44:16
tags:
    - python
    - Apriori
    - 数据挖掘
---
## 前言

做数据分析首先需要数据集进行分析，但是我们没有大规模的数据集，可以采用公开的数据集，我们现在做的是一个关联分析的推荐系统，有一个非常出名的电影数据测试集- [Moivelens](https://grouplens.org/datasets/movielens/).
<!-- more -->
## 选择数据集
里面有很多的大小不同的数据集，我们选择最大的那个`ml-20m`。毕竟我们需要大数据才能复现算法。

![data-set_1](http://ouonrxq3b.bkt.clouddn.com/Apriori/data-set/1.jpg)

## 需要的python库

因为我们暂时不需要可视化，所以只引入了下面的库：

- `pandas`

(写到这里愣了一下，发现只用了一个数据分析库，pandas确实是python的数据分析神器)

> 以下全部使用ipython-notebook运行，最后也会放出总的ipynb文件

## 数据集整理

我们读取我们的数据集

```python

Dir = '/Users/su/Desktop/python_project/ml-20m/ratings.csv'
all_ratings = pd.read_csv(Dir)
```

然后输出发现数据集是以下的格式：

![data-set_2](http://ouonrxq3b.bkt.clouddn.com/Apriori/data-set/2.jpg)

这样的数据结构在`pandas`里面叫`DataFrame`，是一个表格型的数据结构，是左边的数字是行索引(index),还有列索引,我们一般都通过对访问索引来进行列的操作。

最右边的`timestamp`列是时间戳，相比有`:`、`.`的公元纪时更容易存储和使用(只是一串数字)。

> 时间戳的介绍[Unix时间](https://zh.wikipedia.org/wiki/UNIX%E6%97%B6%E9%97%B4)

`pandas`里有时间戳转换的方法`to_datatime`。

```python
# 时间戳转换
all_ratings['timestamp'] = pd.to_datetime(all_ratings['timestamp'], unit='s')
```

将代码运行后，重新将`all_ratings`输出：

![all_ratings](http://ouonrxq3b.bkt.clouddn.com/Apriori/3.png)

我们发现时间转换公元时间了。

## Apriori算法复现

我们要实现的关联规则是

 - 如果一个用户喜欢一个电影，那么他们可能喜欢这部电影。

首先我们要知道，用户是否喜欢这一步电影，我们创建一个新的特征`favorable`,如果用户喜欢这部电影,值为`True`
。

代码:

```python
all_ratings["favorable"] = all_ratings['rating'] > 3
```

我们看一下这个新的特征：

![favorable](http://ouonrxq3b.bkt.clouddn.com/Apriori/4.png)

我们可以先选一部分作为`训练集`,这样能提高训练速度。

```python
# 选取前200作为训练集
# isin是判断矢量化集合的成员资格，返回bool值,以下返回符合资格的成员，返回前200（符合）
ratings = all_ratings[all_ratings['userId'].isin(range(200))]
```

接下来我们新建一个数据集，只包括喜欢电影的数据列。

```python
# 返回一个打分全部高于3.0的dataframe
favorable_ratings = ratings[ratings['favorable']]
```

![favorable](http://ouonrxq3b.bkt.clouddn.com/Apriori/5.png)

我们生成`频繁项集`的时候会搜索用户喜欢的电影，所以我们需要知道每个用户喜欢的电影。我们按照userid分组，并遍历每个用户看过的每一部电影。

```python
# 分组运算 groupby
# favorable_ratings.groupby("userId")["movieId"]是
# favorable_ratings['movieId'].groupby(favorable_ratings["userId"])的语法糖
# k是分组名（用户名），v是数据块（电影id）
# frozenset是不可变集合
favorable_reviews_by_users = dict((k, frozenset(v.values))
                                      for k, v in favorable_ratings.groupby("userId")["movieId"])
```

> 为什么我们使用`frozenset`这种不可变集合，因为这种数据结构没有添加和删除操作，分配的内存小，速度很快，比列表(`list`)、可变集合(`set`)的速度都快。

分组运算的原理可以看下图:

![groupby](http://7xo67b.com1.z0.glb.clouddn.com/2016-06-17/groupby.png)

我们做完之后我们看看效果：

![favorable_reviews_by_users](http://ouonrxq3b.bkt.clouddn.com/Apriori/6.png)

现在我们知道每个影迷所喜欢的电影了，然后我们再对每部电影拥有的影迷进行计数：

```python
# 获取影迷数量求和,对每部电影进行支持度计数（）
num_favorable_by_movie = ratings[["movieId", "favorable"]].groupby("movieId").sum()

```
![num](http://ouonrxq3b.bkt.clouddn.com/Apriori/7.png)

做了前面的准备之后到现在我们开始实现`Apriori`算法

我们创建一个`频繁项集`，并且设定`最小支持度`。

```python
frequent_itemsets = {}  # 频繁项集
min_support = 50    # 最小支持度
```

我们开始生成初始的`频繁项集`，就是`1维频繁项集`，

```python
frequent_itemsets[1] = dict((frozenset((movie_id,)), row['favorable'])
                            for movie_id, row in num_favorable_by_movie.iterrows()
                            if row["favorable"] > 50)
```
> 无论是列表推导式和字典推导式在这次复现都会大量出现。

![frequent](http://ouonrxq3b.bkt.clouddn.com/Apriori/8.png)

我们创建下面的这样一个函数：

```python

def find_frequent_itemsets(favorable_reviews_by_users, k_1_itemsets, min_support):
    counts = defaultdict(int)
    for user, reviews in favorable_reviews_by_users.items():
        for itemset in k_1_itemsets:
            if itemset.issubset(reviews):
                for other_reviewed_movie in reviews - itemset:
                    current_superset = itemset | frozenset((other_reviewed_movie,))）
                    counts[current_superset] += 1
    return dict([(itemset, frequency)
                for itemset, frequency in counts.items() if frequency >= min_support])
```

这个函数的功能是传入一个`k维的频繁项集`，返回一个`k+1维的频繁项集`
。

我们一步步对这个函数进行解析，传入3个参数,第一个是之前使用`groupby`分组运算分组出来的分组,第二个是`k维项集`,第三个是最小支持度。

---

```python
counts = defaultdict(int)
```
- 生成了一个默认为0的带key的数据字典,values的值具有默认值，keys值自定。

```python
for user, reviews in favorable_reviews_by_users.items():
```
- 遍历影迷和他们喜欢的电影集合

```python
        for itemset in k_1_itemsets:
            if itemset.issubset(reviews):
```

- `issubset`是判断子集函数,判断`itemset`是否是`reviews`的子集,如果是则表示用户已经为该电影打分。

```python
 for other_reviewed_movie in reviews - itemset:
 	 current_superset = itemset | frozenset((other_reviewed_movie,))
 	 counts[current_superset] += 1
```
- 遍历用户打过分却没有出现过在项集里面的电影,然后生成`候选项集`,
`itemset | frozenset((other_reviewed_movie,))`是并集。然后我们进行`支持度计数`。`counts[current_superset] += 1`这样的集合出现一次我们就进行一次+1,`支持度计数`
。

> `cout`这个数据字典上面有介绍,我们使用每个候选项集作为键,支持度为值。

```python
return dict([(itemset, frequency)
                for itemset, frequency in counts.items() if frequency >= min_support])
```
最后在return函数检测`频繁程度`,返回其中的频繁项集。

---

一个简单的k维for循环:

```python
for k in range(2, 20):
    cur_frequent_itemsets = find_frequent_itemsets(favorable_reviews_by_users,
                                                   frequent_itemsets[k-1],
                                                   min_support=min_support)
    frequent_itemsets[k] = cur_frequent_itemsets
```

执行完后我们得到多个维的`频繁项集`,我们最多只发现了6维的频繁项集。

![frequent-all](http://ouonrxq3b.bkt.clouddn.com/Apriori/9.png)

我们把1维的项集去掉,因为生成关联规则需要两个项目。

```python
del frequent_itemsets[1]
```

创建一个列表存储关联规则

```python
candidate_rules = []    # 关联规则
```

我们遍历我们生成的`频繁项集`,第一层for循环把`k维项集`里面的`频繁项集`取出，第二层for循环获得每一个项集，第三层遍历项集内数据。

我们使用前提和结论作为规则，结论仅仅只为一个值得推荐的电影。前提为`频繁项集`内的项目。


```python
for itemset_length, itemset_counts in frequent_itemsets.items():
    for itemset in itemset_counts.keys():
        for conclusion in itemset:
            premise = itemset - set((conclusion,))
            candidate_rules.append((premise, conclusion))
```

我们输出我们的关联规则看看:

![candidate](http://ouonrxq3b.bkt.clouddn.com/Apriori/10.png)

获得关联规则后，我们还需要计算`置信度`，`置信度`为规则应验的次数。

首先我们建两个`defaultdict`字典用来存储规则应验的次数。

```python
# 存储规则应验的次数
corrent_couts = defaultdict(int)
# 不应验的次数
incorrect_couts = defaultdict(int)
```

开始计算置信度:

```python
for user, reviews in favorable_reviews_by_users.items():
    for candidate_rule in candidate_rules:
        premise, conclusion = candidate_rule
        # 判断是否喜欢前提电影
        if premise.issubset(reviews):
            # 判断是否喜欢结论电影
            if conclusion in reviews:
                corrent_couts[candidate_rule] += 1
            else:
                incorrect_couts[candidate_rule] += 1
# 置信度计算 P(B|A) = P(AB) / P(A)，每条规则的置信度
rule_confidence = {candidate_rule: corrent_couts[candidate_rule] / float(corrent_couts[candidate_rule] + incorrect_couts[candidate_rule])
                   for candidate_rule in candidate_rules}
```

然后我们对置信度进行排序：

```python
sorted_confidence = sorted(rule_confidence.items(), key=itemgetter(1), reverse=True)
```

> `sorted`函数使用可以看这里[sorted函数](https://www.cnblogs.com/sysu-blackbear/p/3283993.html)

Apriori算法的训练生成关联规则暂时到这里就结束了。接下来我们要用剩下来的从数据集作为测试集。

> 

## 评估关联规则

我们首先将关联规则置信度高的前5位输出:

```python
for index in range(5):
    print("规则 #{0}".format(index + 1))
    (premise, conclusion) = sorted_confidence[index][0]
    print("关联规则:如果一个人喜欢 {0} 他们应该也会喜欢 {1}".format(premise, conclusion))
    print("- 置信度:{0:.3f}".format(rule_confidence[(premise, conclusion)]))
    print('\n')
```

我们使用之前的关联规则对剩下的数据集进行置信度排序:

```python
test_dataset = all_ratings[~all_ratings['userId'].isin(range(200))]
test_favorable = test_dataset[test_dataset["favorable"]]
test_favorable_by_users = dict((k, frozenset(v.values)) for k, v in test_favorable.groupby("userId")["movieId"])

correct_counts = defaultdict(int)
incorrect_counts = defaultdict(int)

for user, reviews in test_favorable_by_users.items():
    for candidate_rule in candidate_rules:
        premise, conclusion = candidate_rule
        if premise.issubset(reviews):
            if conclusion in reviews:
                correct_counts[candidate_rule] += 1
            else:
                incorrect_counts[candidate_rule] += 1

test_confidence = {candidate_rule: correct_counts[candidate_rule] / float(correct_counts[candidate_rule] + incorrect_counts[candidate_rule])
                   for candidate_rule in rule_confidence}

sorted_test_confidence = sorted(test_confidence.items(), key=itemgetter(1), reverse=True)
```

获得测试集的置信度后，我们将前5条输出:

```python
for index in range(5):
    print("规则 #{0}".format(index + 1))
    (premise, conclusion) = sorted_confidence[index][0]
    print("关联规则:如果一个人喜欢 {0} 他们应该也会喜欢 {1}".format(premise, conclusion))
    print(" - 训练集 置信度: {0:.3f}".format(rule_confidence.get((premise, conclusion), -1)))
    print(" - 测试集 置信度: {0:.3f}".format(test_confidence.get((premise, conclusion), -1)))
    print("")
```
## 未完待续





