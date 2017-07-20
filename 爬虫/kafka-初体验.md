最近学习了一个分布式爬虫系统 Frontera，其中通信载体使用的是 kafka。之前只是听说 kafka 很 🐂，但是没有实践过，借这个机会入门一下，也想在自己的爬虫系统中引入kafka。再次记录一下自己的学习过程和理解，希望对其他初学者能有帮助。

###一、阅读文章
google关键词“kafka”，把排在前几位的文章阅读一下，对kafka有了初步的认识，觉得有用的文章（我一般学习都是先中文文档了解，英文文档深入）：
[Kafka背景及架构介绍](http://www.infoq.com/cn/articles/kafka-analysis-part-1)
[kafka官方文档](https://kafka.apache.org/intro)
[Kafka文件存储机制那些事](http://tech.meituan.com/kafka-fs-design-theory.html)
之后是实践python如何使用kafka，参考：
[kafka-python](https://github.com/dpkp/kafka-python)
[kafka-python 文档](http://kafka-python.readthedocs.io/en/master/)

###二、我的思考
在阅读中想到的一些问题，和我的解答
#####1、kafka是什么？
我的理解是 ：
> kafka 是一个分布式的消息队列，也是一个分布式的发布-订阅系统，还是一个分布式存储系统。

#####2、多个 partition 在 多个 broker 中如何分布？
平均分布。partition 会尽量分配在每一个broker中。

#####3、partition 和 customer 的对应关系？
一个partition只能对应一个customer，即最多只有一个customer能从一个特定的partition取数据。
######(1) 当customer中指定partition时：
customer会从对应的partition中获取数据（如果没有别的customer正在消费这个partition）。
######(2) 当customer中没有指定partition时：
如果customer数量多于partition数量，则有的customer会没有partition对应；反之，partition会平均分配给customer。

#####4、kafka-python 中 producer 存数据时如何指定partition? 
通过partitioner指定一个回调函数来决定。默认通过key计算hash值分配到不同分区；如果没有key，则随机分配。

#####5、kafka-python 中能创建topic吗？
 如果服务器端允许自动生成topic的话是可以的，但是不推荐，标准方法还是在server端使用"kafka-topics.sh"创建。

###三、python kafka
示例代码：
customer.py
```
#coding:utf8
from kafka import KafkaConsumer, TopicPartition
consumer = KafkaConsumer(
                         group_id='my-group2',
                         bootstrap_servers=['localhost:9092','localhost:9093','localhost:9094'])
consumer.subscribe(('test-topic1'))
 consumer.assign(_partition_ids)
for message in consumer:
    print ("%s:%d:%d: key=%s value=%s" % (message.topic, message.partition,
                                          message.offset, message.key,
                                          message.value))
```
producer.py
```
#coding:utf8
from kafka import KafkaProducer
from kafka.partitioner.default import DefaultPartitioner
producer = KafkaProducer(bootstrap_servers=['localhost:9092','localhost:9093','localhost:9094'])
print producer.partitions_for('test-topic1')
for _ in range(3):
    producer.send('test-topic1', key=None, value=b'msg')
```

###四、生产环境中
还没有做，方向是这个样子的：
######1、安全验证
连接的时候需要验证，数据传输的时候可能需要加密（我司用的是ssl，具体参考官方文档"security"部分）。
######2、优化配置
阅读相关文章优化配置，最大程度发挥kafka的性能。
