项目中用twisted写了一个http服务器，其中不可避免的有些耗时较长的函数，为了更好的提高并发，今天进行了调研。

####开始我想用Deferred
通过今天的调研，我发现之前对defer的理解是不对的：
>1）Deferred是**不能**让阻塞的代码不阻塞，twisted中提供了deferToThread来实现这个需求
2）Why Deferred？twisted中一些耗时的操作不能及时返回result，而是返回一个Deferred对象，后续可以通过Deferred对象获取result。
3）deferToThread的pool默认最小是5最大是10，可以通过suggestThreadPoolSize设置

####实现代码
代码地址：
>https://github.com/xsren/python_test_demo

```
#coding:utf8

from twisted.web.server import Site
from twisted.web.resource import Resource
from twisted.internet import reactor,threads
from twisted.web.server import NOT_DONE_YET
import time

class CrawlerServer(Resource):

    def __init__(self):
        pass

    def render_GET(self, request):
        print "start......"
        d = threads.deferToThread(self.test)
        d.addCallback(self.succeeded,request)
        d.addErrback(self.failed,request)
        print "finish......"
        return NOT_DONE_YET

    def succeeded(self, result, request):
        print result,request
        request.write("<html><body>Sorry to keep you waiting.</body></html>")
        request.finish()

    def failed(self, failure, request):
        return failure

    def test(self):
        print 'test'
        time.sleep(15)

if __name__ == '__main__':
    
    root = Resource()
    root.putChild("crawler", CrawlerServer())
    factory = Site(root)
    reactor.listenTCP(1234, factory)
    reactor.run()

```

####测试
```
#coding:utf8
import requests
import threading

def run():
    print requests.get('http://127.0.0.1:1234/crawler')

def main():
    t_list = []
    for i in xrange(30):
        t_list.append(threading.Thread(target=run,args=()))
    for t in t_list:
        t.start()
    for t in t_list:
        t.join()
        
if __name__ == '__main__':
    main()
```

####小插曲
一开始我用浏览器测试，发现是串行的。。。在网上查发现浏览器对同一域名的并发请求是有限制的。
