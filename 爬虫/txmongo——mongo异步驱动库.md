这几天媳妇回娘家了，自己和同学熬夜打了几次dota，感觉自己直接傻X了。流鼻涕、眼睛干涩流眼泪，网上说这是过敏性鼻炎的问题，~~~~(>_<)~~~~。身体是革命的本钱啊，还是要早睡早起。。。

---

我们的爬虫服务器是 twisted + mongo 实现的，之前用的是pymongo，这是同步的，当数据量大的时候，耗时的请求会阻塞其他的请求，这是不能忍的。在组会上我同事介绍了[ **txmongo** ](https://github.com/twisted/txmongo)，我决定实验一下。下面是我跑通的demo。

#### 实验过程
使用 twisted xmlrpc 实现 server 端， 使用 [ **xmlrpc** ](https://docs.python.org/2/library/xmlrpclib.html)调用 server 端的函数。

####实验代码
##### server代码
```
#coding:utf8
from twisted.web.server import Site
from twisted.web.resource import Resource
from twisted.internet import defer,reactor
import txmongo
from twisted.web import xmlrpc

class CrawlerServer(xmlrpc.XMLRPC):

    allowNone = True

    def __init__(self):
        xmlrpc.XMLRPC.__init__(self)
        mongo = txmongo.MongoConnection()
        foo = mongo.foo  # `foo` database
        self.test = foo.test  # `test` collection

    @defer.inlineCallbacks
    def xmlrpc_insert(self,doc):
        result = yield self.test.insert(doc, safe=True)
        defer.returnValue(repr(result))

    @defer.inlineCallbacks
    def xmlrpc_find(self,spec,limit=10):
        result = yield self.test.find(spec, limit=limit)
        defer.returnValue(repr(result))

if __name__ == '__main__':
    root = Resource()
    root.putChild("xmlrpc", CrawlerServer())
    factory = Site(root)
    reactor.listenTCP(8888, factory)
    reactor.run()
```

##### client代码
```
import xmlrpclib

srv = xmlrpclib.Server("http://localhost:8888/xmlrpc",allow_none=True)
print "insert:", srv.insert({"name":"foobar","useDateTime":1})
print "find:", srv.find({"name":"foobar","useDateTime":1})

```

#### 运行代码
server
> python txmongo_server.py 

client
> python txmongo_client.py

结果：

![运行结果.png](http://upload-images.jianshu.io/upload_images/3781366-d8fd2875b8302fe7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 代码下载

[测试代码位置](https://github.com/xsren/python_test_demo/tree/master/test_txmongo)
