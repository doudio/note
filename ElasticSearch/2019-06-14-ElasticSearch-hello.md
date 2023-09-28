> ## [ElasticSearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)

> ### Linux 安装步骤

> 准备1.8jdk，安装1.8以上版本(linux5.X以上版本依赖1.8以上的jdk)。

> 导入Elasticsearch PGP Key
>
> ```shell
> rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
> ```

> 安装 Elasticsearch 的 rpm 库使用 `ll /etc/yum.repos.d/` 查看当前机器上已经安装的 rpm 库，如果没有Elasticsearch的话需要创建。用vim创建新的repo文件。
>
> > ```shell
> > vim /etc/yum.repos.d/elasticsearch.repo
> > ```
> >
> > >  内容
> >
> > ```shell
> > [elasticsearch-5.x]
> > name=Elasticsearch repository for 5.x packages
> > baseurl=https://artifacts.elastic.co/packages/5.x/yum
> > gpgcheck=1
> > gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
> > enabled=1
> > autorefresh=1
> > type=rpm-md
> > ```

> 安装Elasticsearch 安装好Elasticsearch 的repo后，就可以通过yum命令安装 Elasticsearch 了。
>
> ```shell
> yum install elasticsearch
> ```

> 启动与测试在CentOS6.5下 由于是init来启动引导用户态程序的，要启动 
>
> 需要通过命令 sudo chkconfig --add elasticsearch 添加在启动服务中。
>
> ```shell
> chkconfig --add elasticsearch
> service elasticsearch start
> ```

> ### 启动后遇见的问题

```java
which: no java in (/sbin:/usr/sbin:/bin:/usr/bin)
Could not find any executable java binary. Please install java in your PATH or set JAVA_HOME
```

> 解决方案
>
> 编辑elasticsearch配置文件，设置JAVA_HOME
>
> ```shell
> vim /etc/sysconfig/elasticsearch
> ```
>
> > 改掉
>
> ```shell
> 将：
> #JAVA_HOME=
> 改成：
> JAVA_HOME=/usr/local/jdk/jdk1.8.0
> ```

> 从新启动后发送请求进行测试
>
> ```shell
> curl -XGET http://127.0.0.1:9200/
> ```
>
> > Result 返回结果
>
> ```json
> [root@localhost opt]# curl -XGET http://127.0.0.1:9200/
> {
>   "name" : "GED59jp",
>   "cluster_name" : "elasticsearch",
>   "cluster_uuid" : "PlWySDQ0SD2kYPCw9fxUlw",
>   "version" : {
>     "number" : "5.6.14",
>     "build_hash" : "f310fe9",
>     "build_date" : "2018-12-05T21:20:16.416Z",
>     "build_snapshot" : false,
>     "lucene_version" : "6.6.1"
>   },
>   "tagline" : "You Know, for Search"
> }
> ```