最近公司产品升级，要做日志集中处理，最终的技术选型是大名鼎鼎的[**ELK**(Elasticsearch + Kibana + Logstash)](https://www.elastic.co/products)。

其中：
```
Logstash 负责日志的手机。
Elasticsearch 提供数据的存储、索引和查询服务。
Kinaba 提供数据展示的功能。
```

一开始我想在本地搭建一套系统体验一下，但是发现网上最新版的中文文档很少，决定写这一篇，记录一下如何在本地搭建ELK系统。

系统：MacOS 10.12.5
Elasticsearch、Logstash、Kinaba都是使用当前最新的5.5.0。

参考文章：https://www.elastic.co/start

# Elasticsearch 安装与配置
访问 https://www.elastic.co/start 下载最新的Elasticsearch，并解压缩。

如下：
![image.png](http://upload-images.jianshu.io/upload_images/3781366-81c25072cd9a7f2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Elasticsearch入门和深入请参考[Elasticsearch: 权威指南](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)。

# Kibana 安装与配置
访问 https://www.elastic.co/start 下载最新的Kibana，并解压缩。

如下：
![image.png](http://upload-images.jianshu.io/upload_images/3781366-dbc6a38b188967ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 安装X-Pack
X-Pack相当于一个和安全、监控相关的插件，安装以后访问elasticsearch就需要密码了。
```
# 在elasticsearch目录
bin/elasticsearch-plugin install x-pack
# 在kibana目录
bin/kibana-plugin install x-pack
```

# 运行
```
# 在elasticsearch目录
bin/elasticsearch
# 在kibana目录
bin/kibana
```

# 打开kibana 
url：http://localhost:5601
用户名: elastic 密码: changeme

# logstash配置
前面都很顺利，logstash配置废了我一点时间，因为网上都多数都是收集nginx日志的例子，而我需要收集自己定义的日志。

我爬虫输出的日志格式：
```
2017-08-01 11:27:28,626 run[line:47] [INFO]: nhmsirqlfbdzmi
2017-08-01 11:27:33,628 run[line:47] [INFO]: cvwosynxhubeb
2017-08-01 11:27:38,629 run[line:47] [INFO]: bjsdznughm
......
```

下面是我成功的配置：
config/logstash-indexer.conf 
```
input {
 file {
   path => ["/tmp/spider.log"]
 }
}

filter {
    grok {
        patterns_dir => "/Users/xsren/install/logstash-5.5.0/config/patterns"
        match => {
            "message" => "%{LOG_TIME:logtime}\s%{FUNC_NAME:func}\[line:%{LINE_NUM:line}\]\s\[%{LOG_LEVEL:log_level}\]:\s%{LOG_INFO:info}"
        }
    }
    mutate {
     remove_field => [ "message" ]
    }
}

output {
  elasticsearch { 
    hosts => ["localhost:9200"] 
    user => elastic
    password => changeme
  }
  stdout { codec => rubydebug }
}%
```
config/patterns  
```
LOG_TIME \d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2},\d{3}
FUNC_NAME \w+
LINE_NUM \d+
LOG_LEVEL \w+
LOG_INFO \w+%  
```
运行logstash
```
./bin/logstash -f config/logstash-indexer.conf
```
运行python脚本生成日志：
```
python test_log.py
```
具体的脚本和配置文件大家可以从[我的github](https://github.com/xsren/python_test_demo/tree/master/test_logger)下载，欢迎叉我（fokk）和赞我（start）。
kinaba中的查询结果：
![image.png](http://upload-images.jianshu.io/upload_images/3781366-9537223ce5ed0a4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

测试logstash日志格式的时候，网上推荐用这个网站：http://grokdebug.herokuapp.com/

这是我测试成功的图片：
![image.png](http://upload-images.jianshu.io/upload_images/3781366-224a005d345aeb7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
