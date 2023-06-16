> ## ElasticSearch Plug in unit

#### linux安装head插件：

* 也可以使用这个 https://github.com/lmenezes/cerebro.git

>  1、选择一个node.js版本

```shell
# 4.x版本
curl --silent --location https://rpm.nodesource.com/setup_4.x | bash -
# 5.x版本
curl --silent --location https://rpm.nodesource.com/setup_5.x | bash -
```

> 然后执行

```shell
yum install -y nodejs
```

>  2、安装git

```shell
yum install git
```

> 3、下载head源码

```shell
mkdir es-head
cd es-head
git clone git://github.com/mobz/elasticsearch-head.git
```

> 4、安装head

```shell
cd elasticsearch-head
npm install
```

> 5、设置允许连接head服务，修改head下的Gruntfile.js，找到server项进行修改

```shell
vim /usr/local/es/elasticsearch-head/Gruntfile.js
#在server项加上如下设置项（允许任何IP访问该服务）
hostname:'*'
```

>  6、设置外网IP访问时，连接elasticsearch地址

```shell
vi /usr/local/es/elasticsearch-head/_site/app.js

找到http://localhost:9200修改成http://外网IP:9200即可
```

> 7、运行

```
npm run start
```