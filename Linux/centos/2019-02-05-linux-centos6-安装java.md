> ## linux 安装 java

> 将 jdk 压缩包解压

```shell
[root@localhost Downloads]# tar zxvf jdk-8u191-linux-x64.tar.gz 
```

> 将解压好的 jdk `cp` 到目标目录

```shell
[root@localhost Downloads]# cp -r jdk1.8.0_191/ /usr/local/jdk8
```

> 编辑 profile 环境变量

```shell
vi /etc/profile
```

> 添加 jdk 环境内容

```shell
JAVA_HOME=/usr/local/jdk8

JRE_HOME=/usr/local/jdk8/jre

CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib

PATH=$JAVA_HOME/bin:$PATH

export PATH JAVA_HOME CLASSPATH
```

> 生效环境变量

```shell
source /etc/profile
```

