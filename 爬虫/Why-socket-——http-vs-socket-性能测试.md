###一、背景
为了提高爬虫与爬虫服务器的数据传输效率，将之前的 HTTP 传输改成现在的 socket 传输。之前只知道 socket 的传输效率高于 http，但是不知道究竟高多少，今天写了 demo 简单测试了一下。
###二、知识准备
先理解一下 HTTP、TCP/IP、Socket的概念（参考文章 http://itindex.net/detail/47119-socket-http）
>HTTP协议：简单对象访问协议，对应于应用层  ，HTTP协议是基于TCP连接的
>TCP协议：    对应于传输层
>IP协议：     对应于网络层  
>TCP/IP是传输层协议，主要解决数据如何在网络中传输；而HTTP是应用层协议，主要解决如何包装数据。

>Socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口（API），通过Socket，我们才能使用TCP/IP协议。

socket 相当于一次连接，然后发送数据，直到数据发送完毕才断开连接。而 HTTP 相当于每次请求都建立连接，发送完数据，就关闭。主要的时间浪费在了建立连接上。

###三、实验
#####1、环境
python 2.7.10
twisted 16.4.0
#####2、过程
使用 twisted 建立一个 socket server 和一个 http server，测试分别向两个 server 发送10000次数据花费的时间。
#####3、代码
代码地址：
>https://github.com/xsren/python_test_demo

http_serve.py
```
#coding:utf8
from twisted.web.resource import Resource
from twisted.web.server import Site
from twisted.web import resource
from twisted.internet import reactor

class Test(resource.Resource):
    def render_POST(self, request):
        return "<html>Hello, world!</html>"

root = Resource()
root.putChild("test", Test())
factory = Site(root)
port = 8081
print 'run server on %s' % port
reactor.listenTCP(port, factory)
reactor.run()
```

socket_server.py
```
#coding:utf8
from twisted.internet.protocol import Factory
from twisted.protocols.basic import LineReceiver
from twisted.internet import reactor

class Test(LineReceiver):

    def dataReceived(self, data):
        self.transport.write("<html>Hello, world!</html>")


class TestFactory(Factory):

    def buildProtocol(self, addr):
        return Test()

if __name__ == '__main__':
    port = 8080
    print 'listen on %s' %port
    reactor.listenTCP(port, TestFactory())
    reactor.run()
```

send_message.py
```
#coding:utf8
import time
import functools
import requests
import json
import socket

data = json.dumps({'aaa':'bbb'})
count = 10000

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        t0 = time.time()
        res = func(*args, **kw)
        t_diff = time.time() - t0
        print "%s, use time: %s"%(func.__name__, t_diff)
        return res
    return wrapper

@timer
def send_to_http_server():
    url = 'http://127.0.0.1:8081/test'
    for i in xrange(count):
        requests.post(url, data=data)

@timer
def send_to_socket_server():
    # 创建一个socket:
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # 建立连接:
    s.connect(('127.0.0.1', 8080))
    for i in xrange(count):
        s.send(data)
        recv(s)
    # 关闭连接:
    s.close()

def recv(s):
    # 接收数据:
    while True:
        # 每次最多接收1k字节,这里简化了过程，实际生产环境中会更复杂
        d = s.recv(1024)
        return d

if __name__ == '__main__':
    send_to_http_server()
    send_to_socket_server()
```

######4、运行
先启动 http server 和 socket server， 然后启动客户端。
```
python http_server.py
python socket_server.py
python send_message.py
```
######5、结果

![结果.png](http://upload-images.jianshu.io/upload_images/3781366-4b3ec5de833b6448.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 可以看出，在发送1w次的情况下，使用socket的效率是http的20倍左右，所以为了提高效率还是要用socket的。
