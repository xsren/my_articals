前一段时间做了把QtWebEngine加入到 [spynner](https://github.com/makinacorpus/spynner)中的工作。因为之前对Qt、WebKit、WebEngine不熟，导致工作中踩了很多坑，特此记录一下，希望后人能避免这些坑。

-------

# 一、起因

做爬虫的时候会遇到对面反制很严格的情况，这种情况下简单的抓包已经不能解决问题了，需要使用代码操控浏览器来抓取网页。

我司用的 [spynner](https://github.com/makinacorpus/spynner)，这是一个基于PyQT和WebKit的开源python浏览器模块。

但是Webkit支持H5特性太少，已经无法满足我们的需求（比如在抓取linkedin的时候，直接判断我们的浏览器版本太低，不返回数据）。从Qt5.4开始，Qt引入了WebEngine，从Qt5.6开始删除了Webkit。所以决定让我把WebEngine加入到synner中。

# 二、概念和历史
http://web.jobbole.com/84826/ 这篇文章讲了现在主流浏览器内核的历史，推荐阅读。

## 1、浏览器内核
浏览器的核心部分，主要负责对网页语法的解释（如[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)下的一个应用[HTML](https://baike.baidu.com/item/HTML)、[JavaScript](https://baike.baidu.com/item/JavaScript)）并渲染（显示）网页。

## 2、Qt
 是一个1991年由Qt Company开发的跨平台[C++](https://baike.baidu.com/item/C%2B%2B)[图形用户界面](https://baike.baidu.com/item/%E5%9B%BE%E5%BD%A2%E7%94%A8%E6%88%B7%E7%95%8C%E9%9D%A2)应用程序开发框架。主要用来开发GUI程序，但是也可以开发无界面的程序。

## 3、WebKit
参考 https://trac.webkit.org/wiki/QtWebKit 。

WebKit是一个开源的浏览器引擎。WebKit的HTML和JS代码来源于KDE开发的KHTML 和 KJS，其中KHTML有一部分依赖Qt（苹果工程师去掉了这部分依赖）。 QtWekKit旨在将WebKit的新特性移植回Qt中。

## 4、WebEngine
WebEngine来源于webkit，webkit之前主要由苹果和谷歌两个公司贡献代码，但是后来google推出自己的新分支blink，即chromium的内核。WebEngine基于的就是chromium。因为blink在性能、跨平台等方面的原因，Qt5.6以后，Qt删除了WebKit部分，转向了WebEngine。而且chromium对H5新特性的支持更好，这也是我们要用QtWebEngine替换QtWebKit的原因。


# 三、开发过程
 从QtWebKit到QtWebEngine的过程，主要有两个难点：
*  之前WebKit的一些函数都变成了异步，需要加上回调
*  很多函数需要对着Qt文档查，WebEngine比较新，网上相关的资料比较少

# 四、坑

## 1、QT5.6以后想安装QtWebKit怎么办？
http://www.jianshu.com/p/05f538ebd4fd
centos下执行
```
yum install qt5-qtwebkit*
```
