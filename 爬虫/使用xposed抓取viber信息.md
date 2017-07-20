目录
###1.基本原理
###2.参照网站和代码
###3.实际应用（使用xposed hook viber）
#### (1) 准备
#### (2) 开始
###### Step1：安装xposed
###### Step2：找到需要hook的函数
###### Step3：编写hook代码
###### Step4：运行

---

---

---
# 1.基本原理

如果用"不要脸"来形容动态注入的话，Xposed 可以称为"显摆着不要脸"。我们知道，Zygote 是所有 Android 应用程序的始祖，所有应用程序的进程都是通过请求 Zygote fork 出来的。Zygote 的启动是由 init 进程通过 init.rc 脚本启动的（可以参考《浅谈 SystemServer》），即通过执行 /system/bin/app_process 启动。
但 Xposed 在安装后会把这个可执行文件替换掉，原本作为 Zygote 进程，要启动的入口是 com.android.internal.os.ZygoteInit.main()，替换后，如果 Xposed 可用则被换为了 de.robv.android.xposed.XposedBridge.main()，在这里完成各个模块的初始化。
加载各个模块时，其实就是对 Hook 点的初始化，这里所谓的 Hook 点就是要 Hook 的函数，也是 Xposed 的价值体现。Xposed 会将 Hook 点由 java 函数变为 native 函数，并将其注册到它自己的 C++ 实现函数上，这样每次在执行 Hook 点时，都会执行到它的 C++ 函数中，然后再调回 java 层的真实实现，这个过程看起来跟动态代理模式一样一样的。
一句话总结：**xposed可以hook住所有android中所有的函数**。

# 2.参照网站和代码
我主要是参照下面三个网页学习的
http://3dobe.com/archives/113/
http://www.qingpingshan.com/rjbc/az/37346.html
[赵四大神，我就是参照他的文章入门的](http://www.wjdiankong.cn/android%E4%B8%ADxposed%E6%A1%86%E6%9E%B6%E7%AF%87-%E5%88%A9%E7%94%A8xposed%E6%A1%86%E6%9E%B6%E5%AE%9E%E7%8E%B0%E6%8B%A6%E6%88%AA%E7%B3%BB%E7%BB%9F%E6%96%B9%E6%B3%95/)

# 3.实际应用（使用xposed hook viber）

###（1） 准备：
root手机一部
android 4.4原生系统（不是原生系统安装xposed容易安装失败）
xposed v33 (即[de.robv.android.xposed.installer_v33_36570c.apk](http://dl-xda.xposed.info/modules/de.robv.android.xposed.installer_v33_36570c.apk))
viber ([viber-6-3-0-1702.apk](https://apkplz.com/android-apps/viber/viber-6-3-0-1702-apk-download))
android studio 或者eclipse （我用的是android studio）
 ###（2）开始
######Step1：安装xposed
具体参考[网页](http://www.qingpingshan.com/rjbc/az/37346.html)，只要系统对就没问题。

######Step2：找到需要hook的函数
  首先反编译viber代码，通过JD-GUI查看找到可疑函数，然后通过smali动态调试确认函数是否包含想要的信息。这里就不展开了。

######Step3：编写hook代码
（1） 新建项目，选择 Empty Activity，如下图：

![创建项目](http://upload-images.jianshu.io/upload_images/3781366-7090bccbfced475e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（2）新建完成后,下载 XposedBridgeApi-54.jar 并放入app目录下的libs文件夹.

（3）找到 app 目录下的 build.gradle 文件,将 dependencies 中的
`compile fileTree(dir: 'libs', include: ['*.jar'])`
改为:
`provided fileTree(dir: 'libs', include: ['*.jar'])`
并且需要点一下sync now，如下图：

![修改gradle文件](http://upload-images.jianshu.io/upload_images/3781366-a2a29e6efea52d2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（4）在 AndroidManifest.xml 文件的 application 中添加如下代码,其中的54是前面下载的文件中的号码.
```
<meta-data
    android:name="xposedmodule"
    android:value="true" />
<meta-data
    android:name="xposeddescription"
    android:value="test hook viber" />
<meta-data
    android:name="xposedminversion"
    android:value="54" />
```
（5）编写代码
如下图：代码很简单，看注释就行。

![代码](http://upload-images.jianshu.io/upload_images/3781366-238612ace4c026bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（6）新建 assets 文件夹,在里面新建文件名为 xposed_init ,写入刚刚的类名,此处应为 com.example.xsren.myapplication.Main.（这一步很重要千万别忘了。。）
如下图：
![新建xposed_init](http://upload-images.jianshu.io/upload_images/3781366-00e88e68de6da50c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Step4：运行
(1) 安装apk后xposed提示激活插件，如下图：
![xposed提示激活插件](http://upload-images.jianshu.io/upload_images/3781366-65f63b5b5e371761.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(2) 激活插件，如下图：
![激活插件](http://upload-images.jianshu.io/upload_images/3781366-d1197a5e5ee5a564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(3) 重启，如下图：
![重启](http://upload-images.jianshu.io/upload_images/3781366-ee3c6750090268fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(4) 启动VPN，启动viber，更改通讯录，触发hook的函数

(5)在shell中过滤log，如下图：
![过滤log](http://upload-images.jianshu.io/upload_images/3781366-c39bda73e0ed6abe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

***

---
**代码下载：**
[代码百度网盘下载](http://pan.baidu.com/s/1pKZDuen)


**THE END**
