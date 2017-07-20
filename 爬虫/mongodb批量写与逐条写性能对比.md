大家过年好！！！
终于忙完了年，可以安心写这一篇文章了。
年前有一个爬虫项目，其中一个存储中间件是我用twisted写的，一开始数据量比较少，所以采用的策略是单条写。但是放假前临时加大了数据量，发现中间件处理不过来了！最后将插入和更新操作改成了批量操作解决了这个问题。

####一、先列出结论：

>mongodb写操作的主要时间是耗费在程序与mongodb的通信过程，而不是实际的写过程。所以在数据量很大的时候需要将程序改成批量写。

####二、实验
######1、实验过程
使用pymongo编写一个测试脚本，分别测试在批量写和单条写的情况下插入10w条数据用的时间。
######2、实验环境
mongodb 3.2.9
python 2.7
pymongo 3.3.0
######3、实验代码
代码地址：
>https://github.com/xsren/python_test_demo

```
#coding:utf-8
from pymongo import MongoClient
from pymongo import InsertOne
import time

def test_insert():
    mc = MongoClient("127.0.0.1",maxPoolSize=None)
    db = mc['test']

    # 逐条写
    t0 = time.time()
    for i in xrange(0,100000):
        db['test_1'].insert_one({'_id':i,'x':1})
    print time.time() - t0

    time.sleep(1)

    # 批量写
    t0 = time.time()
    res =[]
    for i in xrange(0,100000):
        res.append(InsertOne({'_id':i,'x':1}))
    db['test_2'].bulk_write(res)
    print time.time() - t0


if __name__ == '__main__':
    test_insert()
```
######4、实验结果
![程序运行结果.png](http://upload-images.jianshu.io/upload_images/3781366-916c6e35f25ea05b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![mongodb运行情况.png](http://upload-images.jianshu.io/upload_images/3781366-6f98503d12be3d8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>从图中可以看出：
1、批量写与单条写差了一个数量级
2、mongo的并发性能还是不错的，在我的机器上可以达到每秒7w+
