最近在学习scrapy，其中有四个重要的组件：Extension、Download Middleware、Spider Middleware、Item Pipeline。我有两个疑问：
```
1、这四个组件初始化的顺序是怎样的？
2、这四个组件接收signal的顺序是怎样的？
```
因为官方文档没有明确指出，我决定自己写一个 scrapy project 测试一下。

### 实验环境
python 2.7.10
scrapy 1.3.3

### 实验过程
自定义四个组件，在其中打log，观察执行顺序。为了方便测试，使用flask自己搭了一个简单的 web app。
###### 启动 web app
`
python app.py
`
###### 启动scrapy
`
scrapy crawl quotes
`
#### 实验结果


![组件初始化.png](http://upload-images.jianshu.io/upload_images/3781366-391efbcbf81371da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![组件接收signal.png](http://upload-images.jianshu.io/upload_images/3781366-1af90b816bdcfdf2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 实验结论：
```
1、从图中可以看出初始化的顺序是 Extension、Download Middleware、Spider Middleware、Item Pipeline。
2、控件接收signal的顺序也是 Extension、Download Middleware、Spider Middleware、Item Pipeline。
```
这种顺序也是有一定道理的,参考[scrapy 架构](https://doc.scrapy.org/en/latest/topics/architecture.html)的数据流向，我们知道：
```
1、Download Middleware 主要负责与request相关的操作
2、Spider Middleware主要负责与response相关的操作
当然它们都可以处理
```
![scrapy 架构图.png](http://upload-images.jianshu.io/upload_images/3781366-5cb275cb81b708da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###补充：
>items go through from lower valued to higher valued classes. It’s customary to define these numbers in the 0-1000 range.

Item Pipeline的执行顺序是按照数字从小到大执行

>the [process_spider_input()
](https://doc.scrapy.org/en/latest/topics/spider-middleware.html#scrapy.spidermiddlewares.SpiderMiddleware.process_spider_input) method of each middleware will be invoked in increasing middleware order (100, 200, 300, ...), and the [process_spider_output()
](https://doc.scrapy.org/en/latest/topics/spider-middleware.html#scrapy.spidermiddlewares.SpiderMiddleware.process_spider_output) method of each middleware will be invoked in decreasing order.

Spider Middleware, [process_spider_input() ](https://doc.scrapy.org/en/latest/topics/spider-middleware.html#scrapy.spidermiddlewares.SpiderMiddleware.process_spider_input) 按照从小到大顺序执行， [process_spider_output() ](https://doc.scrapy.org/en/latest/topics/spider-middleware.html#scrapy.spidermiddlewares.SpiderMiddleware.process_spider_output)相反。

>the [process_request()
](https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#scrapy.downloadermiddlewares.DownloaderMiddleware.process_request) method of each middleware will be invoked in increasing middleware order (100, 200, 300, ...) and the [process_response()
](https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#scrapy.downloadermiddlewares.DownloaderMiddleware.process_response)method of each middleware will be invoked in decreasing order.

Download Middleware， [process_request() ](https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#scrapy.downloadermiddlewares.DownloaderMiddleware.process_request)按照从小到大顺序执行，[process_response()](https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#scrapy.downloadermiddlewares.DownloaderMiddleware.process_response)相反。

### 代码下载：
https://github.com/xsren/python_test_demo/tree/master/test_scrapy_mw_exe_seq/
