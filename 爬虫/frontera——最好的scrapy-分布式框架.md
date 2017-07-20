因为公司项目需求，最近在学习[ portia ](https://github.com/scrapinghub/portia)。
>啥是 portia 呢？
portia 是 scrapinghub 团队开源的可视化爬虫框架。有了这个框架，一个不会编程的人直接通过鼠标点击就可以实现网页采集。他们公司已经有了成熟的产品[scrapinghub](https://app.scrapinghub.com/), 功能很强大，可以尝试一下。

在调研 portia 的时候,偶然阅读[ big data at scrapinghub ](http://www.slideshare.net/DanaKelleher/big-data-at-scrapinghub)这篇文章，了解到了[ frontena ](https://github.com/scrapinghub/frontera)。frontera 同样是 scrapinghub 团队的作品——**一个功能强大、可扩展性好、已经在生产环境中得到验证、开源的分布式爬虫框架**。有种相见恨晚的感觉。

### 为什么说是最好的scrapy 分布式框架？？？
通过搜索我发现，scrapy 的分布式框架除了 frontera 还有[ *scrapy-cluster* ](https://github.com/istresearch/scrapy-cluster)和[ *scrapy-redis* ](https://github.com/rolando/scrapy-redis)，综合对比以后发现 frontera 要优于另外两个。
######它支持这些强大的功能：
>1. 面向在线处理，
2. 分布式爬虫和后端架构，
3. 可定制抓取策略，
4. Scrapy易于集成，
5. 集成 [SQLAlchemy](http://www.sqlalchemy.org/) 支持关系型数据库（Mysql， PostgreSQL， sqlite 等等）， 集成 [HBase](http://hbase.apache.org/) 非常好得支持键值对数据库，
6. 使用 [ZeroMQ](http://zeromq.org/) and [Kafka](http://kafka.apache.org/) 为分布式爬虫实现消息总线，
7. 使用 [*Graph Manager*](http://frontera-docs-zh-cn.readthedocs.io/zh_CN/latest/topics/graph-manager.html) 创建伪站点地图和模拟抓取，进行精确抓取逻辑调优。
8. 透明的传输层概念([message bus](http://frontera-docs-zh-cn.readthedocs.io/zh_CN/latest/topics/glossary.html#term-message-bus))和通信协议，
9. 纯 Python 实现 。
10. 支持 Python 3 。

######而且社区活跃度很高
看图说话。
![frontera github.png](http://upload-images.jianshu.io/upload_images/3781366-76bc657d2a317731.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
######而且在生产环境得到验证
看图说话。
![frontera 生产环境](http://upload-images.jianshu.io/upload_images/3781366-1fc4ee51894ca2ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


功能这么强大，有没有心动的感觉，感觉用起来吧。
