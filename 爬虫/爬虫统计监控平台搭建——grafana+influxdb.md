公司爬虫系统需要一个统计和监控平台，可以方便开发和运维查看爬虫抓取的状况和及时收到异常信息报警。

之前的版本是我用flask写的，但是UI较丑陋，功能较简单。。经过我的调研终于找到了grafana+influxdb的解决方案。我个人觉得算的上：UI精美，功能强大。

先介绍一下：
```
# influxdb
是一款开源的时间序列数据库，专门用来存储和时间相关的数据（比如我用它存储某个时间点爬虫抓取信息的数量）
# grafana
是一个开源的分析和监控系统，拥有精美的web UI，支持多种图表，可以展示influxdb中存储的数据，并且有报警的功能
```

这篇文章介绍一下如何在mac下搭建一个测试环境体验一下。
```
MacOS:10.12.5
influxdb:1.2.3
grafana:4.2.0
```

# influxdb 安装和配置
influxdb和mysql的基本语法一样，详情请参考[官方文档](https://docs.influxdata.com/influxdb/v1.3/introduction/getting_started/)，它支持分布式还有HTTP API。

## 安装
使用Homebrew安装
```
brew update
brew install influxdb
```

## 配置
修改  /usr/local/etc/influxdb.conf ，关键位置如下：
```
[data]
  # 存储 TSM 文件的路径，修改成自己的目录。
  dir = "/usr/local/var/influxdb/data"
  # 存储 WAL 文件的路径，修改成自己的目录。
  wal-dir = "/usr/local/var/influxdb/wal"
[admin]
  # 管理界面的host和port
  bind-address = ":8083"
[http]
  # API的host和port
  bind-address = ":8086"
```

# grafana 安装和配置

## 安装
使用Homebrew安装
```
brew update
brew install grafana
```

## 配置
修改 /usr/local/etc/grafana/grafana.ini，关键位置如下：
```
[paths]
# 存储数据、日志、插件的目录
[server]
# 定义host、port等信息
# Protocol (http or https)
;protocol = http

# The ip address to bind to, empty will bind to all interfaces
;http_addr =

# The http port  to use
;http_port = 3000
```

# 爬虫项目中的应用

基本思路是：
```
1、python写一个脚本，定时将mongodb中采集数据相关collection的总量信息写入influxdb
2、grafana连接influxdb数据源
3、在grafana中配置要展示的信息
```

## 1、python脚本
大体的代码如下：
```

def get_from_mongodb(coll_name, name, cond={}):
    return {
        "measurement": name,
        "fields": {
            "value": db[coll_name].count(cond)
        }
    }


def put_to_influxdb(data):
    client.write_points(data)


def run():
    _list = [('angel_people_tasks', 'angel_people_tasks', {}),
             ('angel_companies_tasks', 'angel_companies_tasks', {}),
             ('angel_people_tasks', 'angel_people_tasks_crawled', {'status': 2}),
             ('angel_companies_tasks', 'angel_companies_tasks_crawled', {'status': 2}),

             ('accounts', 'accounts', {}),
             ('proxys', 'proxys_http', {'protocol': 0}),
             ('proxys', 'proxys_socks', {'protocol': 1}),

             ]
    ds = []
    for _l in _list:
        d = get_from_mongodb(_l[0], _l[1], _l[2])
        ds.append(d)

    put_to_influxdb(ds)


def main():
    print 'running.....'
    count = 0
    while True:
        try:
            run()
        except Exception as e:
            print str(e)
            time.sleep(60)
            continue

        print 'finish round %s' % count
        count += 1
        time.sleep(10)


if __name__ == '__main__':
    main()
```
## 2、grafana连接influxdb数据源
在**Data Sources**中点击**add data source**

![image.png](http://upload-images.jianshu.io/upload_images/3781366-08e6d3325fa7043a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3、在grafana中配置要展示的信息
其中的一个：

![image.png](http://upload-images.jianshu.io/upload_images/3781366-657608816131909b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


成功的效果，可以选择时间段来查看：
![image.png](http://upload-images.jianshu.io/upload_images/3781366-6130ccf352e4cf30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

监控在此配置：
![image.png](http://upload-images.jianshu.io/upload_images/3781366-d9579666ecc117df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 后续
grafana也提供了api，后续这些操作都可以自动化。
