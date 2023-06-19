> ### Install Redis

> 在线下载 Linux 包

```linux
[root@localhost ~]# cd /usr/local/src/
[root@localhost src]# wget http://download.redis.io/releases/redis-4.0.6.tar.gz
```

> 离线解压

```linux
[root@localhost src]# tar -zxf redis-4.0.6.tar.gz 
```

> 编译

```linux
[root@localhost redis-5.0.3]$ make
```

> 安装 Redis

```linux
# 将redis安装到/usr/local/redis目录
[root@localhost redis-4.0.6]# make PREFIX=/usr/local/redis install
# 进入redis安装目录
[root@localhost redis-4.0.6]# cd /usr/local/redis/bin/
[root@localhost bin]# ll
total 35432
-rwxr-xr-x. 1 root root 5597214 Aug 30 05:34 redis-benchmark
-rwxr-xr-x. 1 root root 8314825 Aug 30 05:34 redis-check-aof
-rwxr-xr-x. 1 root root 8314825 Aug 30 05:34 redis-check-rdb
-rwxr-xr-x. 1 root root 5736370 Aug 30 05:34 redis-cli
lrwxrwxrwx. 1 root root      12 Aug 30 05:34 redis-sentinel -> redis-server
-rwxr-xr-x. 1 root root 8314825 Aug 30 05:34 redis-server
# 如果出现以上文件说明redis安装成功了
```

> 运行 Redis server

```linux
[root@localhost bin]# ./redis-server 
```

> 连接 Redis

```linux
[root@localhost bin]# ./redis-cli
127.0.0.1:6379> set key value
OK
127.0.0.1:6379> get key
"value"
127.0.0.1:6379> 
```

> 关闭 || 重启 Redis

```linux
# 关闭
[root@localhost bin]# ./redis-cli shutdown

# 重新启动redis加载指定的配置文件
[root@localhost bin]# ./redis-server /etc/redis.conf 
```

>  将redis-server和redis-cli命令加入环境变量

```
vim /etc/proflie
# 在最后一行加入
export PATH=/usr/local/redis/bin:$PATH 
```

> 使其立即生效

```
source /etc/proflie
```

---

> 修改 Redis 默认配置

```linux
# 复制redis.conf文件到/etc目录
[root@localhost redis-4.0.6]# cp /usr/local/src/redis-4.0.6/redis.conf /etc/redis.conf
# 编辑redis.conf文件
[root@localhost ~]# vim /etc/redis.conf 
# 设置redis为后台启动进程，将daemonize no 改为 daemonize yes
daemonize yes 
# 修改绑定的主机地址，将#bind 127.0.0.1改成自己的ip地址，去掉"#"号
bind 192.168.41.22
关闭redis服务，重新运行

[root@localhost bin]# ./redis-cli shutdown
# 重新启动redis加载指定的配置文件
[root@localhost bin]# ./redis-server /etc/redis.conf 
```

