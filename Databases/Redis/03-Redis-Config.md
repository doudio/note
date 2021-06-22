> ### Redis Config

> INCLUDES(包含)

```
include /path/to/other.conf 这在你有标准配置模板但是每个redis服务器又需要个性设置的时候很有用
```

> GENERAL(一般常用)

```
daemonize yes # Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程(yes：后台运行；no：不是后台运行)
```

```
protected-mode yes # 保护模式，默认开启。开启该参数后，redis只会本地进行访问，拒绝外部访问。
```

```
pidfile /var/run/redis/redis-server.pid # redis的进程文件
```

```
port 6379 # redis监听的端口号
```

```
tcp-backlog 511 # TCP连接队列的长度
```

```
bind 127.0.0.1 # 指定redis只接收来自于该IP地址的请求，如果不进行设置，那么将处理所有请求
```

```
timeout 0 # 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
```

```
loglevel notice 
指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose;
debug（很多信息，方便开发、测试）
verbose（许多有用的信息，但是没有debug级别信息多）
notice（适当的日志级别，适合生产环境）
warn（只有非常重要的信息）
```

```
logfile /var/log/redis/redis-server.log # 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null。空字符串的话，日志会打印到标准输出设备。后台运行的redis标准输出是/dev/null
```

```
databases 16 # 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
```

> SNAPSHOTTING(快照)

```
save <seconds> <changes> <指定时间间隔> <执行指定次数更新操作>，满足条件就将内存中的数据同步到硬盘中。
save "" 
save 900 1
save 300 10
save 60 10000
注释掉“save”这一行配置项就可以让保存数据库功能失效，设置sedis进行数据库镜像的频率。
900秒（15分钟）内至少1个key值改变（则进行数据库保存--持久化） 
300秒（5分钟）内至少10个key值改变（则进行数据库保存--持久化） 
60秒（1分钟）内至少10000个key值改变（则进行数据库保存--持久化）
```

```
stop-writes-on-bgsave-error yes # 当RDB持久化出现错误后，是否依然进行继续进行工作，yes：不能进行工作，no：可以继续进行工作，可以通过info中的rdb_last_bgsave_status了解RDB持久化是否有错误
```

