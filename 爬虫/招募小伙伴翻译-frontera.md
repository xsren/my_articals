------20170929 更新------
历时7个月，终于完成了所有的翻译。感谢参与的小伙伴。😀
------



####一、为什么要翻译 frontera ？
frontera 是目前最好的scrapy 分布式框架，不了解frontera的可以参考我写的[ frontera 介绍文章](http://www.jianshu.com/p/ff8846ad0e94)。但是这个框架的知名度却不高（可能是名字的原因，参考 scrapy-redis，scrapy-cluster, 名字有 scrapy 提高了搜索排名，论起名字的重要性），中文文档更是几乎没有。为了服务广大群众，我决定要翻译[ frontera 官方文档](http://frontera.readthedocs.io/en/latest/)。

####二、怎么翻译 frontera？
我已经在github上创建了[存放源代码的项目](https://github.com/xsren/frontera-docs-zh_CN)，在readthedocs创建了[关联项目](http://frontera-docs-zh-cn.readthedocs.io/zh_CN/latest/)。目前只翻译了 index.rst 和 overview.rst 两篇文章， 我现在的翻译速度是每周不少于两篇。
为了提高翻译速度，现在招募小伙伴和我一起翻译剩余的文章。

####三、为什么要参与翻译 frontera ？
1. 参与开源项目，提高自己的社区知名度，为个人简历填上亮眼的一笔，
2. 学习git、github等工具的使用，加入开源社区大家庭中，
3. 同时锻炼自己的翻译水平和技术水平。

So, 如果你也有一颗开源的心，就私信我把，我的简书主页[ xsren ](http://www.jianshu.com/u/39bd3c3d98f1)。

####四、如何参与到翻译中来？

######1、准备
（1）在自己的机器上安装git
（2）注册[ github ](https://github.com/)

######2、fork 项目
项目地址 https://github.com/xsren/frontera-docs-zh_CN
fork 相当于复制了一份原来的项目，你可以在这上面开发，这成了属于你项目分支。
如图：
![fork.png](http://upload-images.jianshu.io/upload_images/3781366-74382643f0ef1e9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

######3、clone 项目到本地
注意是clone的地址是你自己的项目地址，比如你的用户名是 **test2017**，则在命令行中运行：
```
git clone https://github.com/test2017/frontera-docs-zh_CN.git
```
![clone 项目.png](http://upload-images.jianshu.io/upload_images/3781366-f2f76d520ad0574b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



######4、翻译
按照英文，逐行翻译。
![翻译.png](http://upload-images.jianshu.io/upload_images/3781366-dc0a039fe007d416.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

######5、提交代码
在** frontera-docs-zh_CN **的根目录下运行：
```
git status 
git commit -m "update run-modes" .
git pull
git push 
```
![提交代码.png](http://upload-images.jianshu.io/upload_images/3781366-787b4cf4e0ceb536.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

######6、提交 Pull Request
提交代码以后，访问自己的项目主页（例如：https://github.com/test2017/frontera-docs-zh_CN ），会发现有一个可以可以pull request的提示。如图：
![pull request 提示.png](http://upload-images.jianshu.io/upload_images/3781366-922b956b48fd9953.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击 **“Compare & pull request”** 开始提交，进入到下图：

![提交pull request.png](http://upload-images.jianshu.io/upload_images/3781366-92ac4ce1564c26f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击 **Create pull request”** 提交成功。

######7、审核通过
审核通过后，我会执行merge操作，将你的翻译成果加入到项目中，这样你的成果就可以在[ readthedocs ](http://frontera-docs-zh-cn.readthedocs.io/zh_CN/latest/)中看到了。
