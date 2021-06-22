> ## nginx tomcat redis 实现 session 共享

![](img/nginx-tomcat-redis.png)

**准备环境**

* JDK1.8+redis5+tomcat8.5+nginx1.15.8+centos6

##### centos6安装JDK1.8

* 下载JDK1.8.tar.gz包

  * https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

* 在/usr/local目录下创建java目录，将下载的jdk上传至/usr/local/java目录

* 解压jdk安装包

```shell
tar -zxvf jdk-8u191-linux-x64.tar.gz
```

* 设置环境变量

```shell
#编辑环境变量文件profile
vi /etc/profile
######
######在环境变量文件中加入jdk环境变量
JAVA_HOME=/usr/local/java/jdk1.8.0_191
JRE_HOME=/usr/local/java/jdk1.8.0_191/jre
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$JAVA_HOME/bin:$PATH
export PATH JAVA_HOME CLASSPATH
####
#使环境变量生效
source /etc/profile
```

##### 安装tomcat8.5

* 下载tomcat8.5

  * https://tomcat.apache.org/download-80.cgi

* 在centos上新建两个目录：tomcat80，tomcat90,并将下载的tomcat分别上传至新建的两个目录

* 解压安装tomcat

```shell
#1：解压tomcat包
tar -zxvf apache-tomcat-8.5.35.tar.gz
#2：编辑tomcat80、tomcat90下tomcat的context.xml配置文件，在<Context>内容中加入以下内容

<Resources allowLinking="true" />
<Valve className="com.naritech.nicole.gump.RedisSessionHandlerValve" />
<Manager className="com.naritech.nicole.gump.RedisSessionManager"
    host="192.168.108.130"
    port="6379"
    database="0"
    maxInactiveInterval="60"
	/>

##############
#3：编辑tomcat90的下tomcat的server.xml,修改启端口
####
<Server port="8006" shutdown="SHUTDOWN">
<Connector port="8090" protocol="HTTP/1.1"
    connectionTimeout="20000"
    redirectPort="8443" />
<Connector port="8099" protocol="AJP/1.3" redirectPort="8443" />
```

* 将session共享所需要的jar分别导入两个tomcat的lib目录下

  * commons-pool2-2.3.jar
  * jedis-2.7.3.jar
  * tomcat-redis-session-manager-master-2.0.0.jar

* 使用springBoot编写一个最简单的web项目，打成war包，分别放入到两台tomcat的webapps目录下

```java
@GetMapping("/key")
public String key(String key, String value, HttpSession session) {
    Object attribute = session.getAttribute(key);
    if (null == attribute) {
        session.setAttribute(key, value);
        return "not exists(" + key + "::" + value + ")";
    }
    return "exists(" + key + "::" + attribute + ")";
}
```

##### 安装nginx

* 在centos上新建目录:/usr/local/nginx，在当前目录下载nginx

```shell
wget http://nginx.org/download/nginx-1.15.8.tar.gz
```

* 解压nginx包：

```shell
tar -zxvfnginx-1.15.8.tar.gz
```

* 安装PCRE

```shell
yum -y install pcre-devel
```

* 安装zlib

```shell
yum install -y zlib-devel
```

* 进入解压后的nginx-1.15.8目录，执行：./configure 进行nginx配置

* 执行 make install 进行编译安装

* 修改nginx配置文件/usr/local/nginx/conf/nginx.conf

```shell
#配置两台tomcat集群
upstream nginxserver{
    server 192.168.108.130:8080 weight=1;
    server 192.168.108.130:8090 weight=2;
}

server {
    listen       80;
    server_name  192.168.108.130;

    location / {
        root   html;
        index  index.html index.htm;
        proxy_pass http://nginxserver;
    }
｝
```

* 进入/usr/local/nginx/sbin目录下启动nginx:

```shell
./nginx #启动
./nginx -s reload  #重启
./nginx -s stop	#停止
```


##### 验证: 

* 将tomcat、nginx、redis全部启动。访问nginx：192.168.108.130信息服务。
* 在一个服务里写入session,在另一个服务中是否可以获取到 session.