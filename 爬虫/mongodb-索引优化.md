公司爬虫项目中用到了mongodb，最近有一个复杂查询，速度很慢需要优化，决定趁着这个机会好好学习一下mongodb 索引，于是上网查资料。现总结了一下过程。

###1、查资料
首先上网查mongodb index 的原理和使用（从原理上理解会更走更少的弯路，记忆也更加深刻）。
参考：
1、先了解索引
[1.1、这篇文字通过5W1H方式讲索引，很棒！](http://www.ituring.com.cn/article/986)
2、查看如何优化mongodb索引
 [2.1 结合例子讲解如何使用explain命令优化索引](http://blog.csdn.net/defonds/article/details/51377815)
[2.2 explain命令详解，辅助理解2.1](http://itbilu.com/database/mongo/V1WFKy-Cl.html)
[2.3 10gen工程师写的，详细讲解mongodb应用场景](http://www.csdn.net/article/1970-01-01/2811690)

###2、实践
看完上述几篇文章，觉得对mongodb的索引理解差不多了，然后就开整了。
2.1 建索引
```
db.my_coll.createIndex({'localname':1,'km':1,'crawl_time':-1})
```
2.2 查询
```
db.find({'localname':'bj','km':{'$lte':12}}).sort({'crawl_time':-1}).limit(10)
```
结果查询时间还是分钟级的。。肯定是哪里不对。于是我使用explain命令+排除法尝试，发现只要去掉sort就很快。。。这是为什么呢？[这篇文章](http://blog.nosqlfan.com/html/4117.html)给了很好的说明。

引用原文，我建索引依然很慢的原因是:
>所以，在有范围查询（包括$in, $gt, $lt 等等）的时候，其实刻意在后面追加排序索引通常是没有效果的。因为在进行范围查询的过程中，我们得到的结果集本身并不是按追加的这个字段来排的，还需要进行一次额外的排序才行。而在这种情况下，可能反序建立索引（排序字段在前、范围查询字段在后）反而会是一个比较优的选择。当然，是否更优也和具体的数据集有关。

So,我重新建立索引
```
db.my_coll.createIndex({'localname':1,'crawl_time':-1,'km':1})
```

现在查询结果达到毫秒级别了。


***

后记：
记得当时跟我同事讨论的时候，同事跟我说给每个字段建索引更方便。比如：
```
db.my_coll.createIndex({'localname':1})
db.my_coll.createIndex({'km':1})
db.my_coll.createIndex({'crawl_time':-1})
```
这样其实是不可取的，首先是效率没有联合索引高，其次更占存储空间。


