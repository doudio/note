> ## linux sentry

> 监听docker容器

* 基于指令 `docker inspect --format '{{.State.Running}}' <docker-name>` 

```shell
#!/bin/bash
# 获取当前执行的时间并格式化
now=`date +"%Y-%m-%d %H:%M:%S"`
# 迭代每一个容器名
for cn in docker_name1 docker_name2 docker_name3
do
  # 查看进程是否存在
  exist=`docker inspect --format '{{.State.Running}}' $cn`
  if ["${exist}" == "false"];then
    docker start $cn
	echo "${now} 重启docker容器, 容器名: $cn" >> ./docker_monitor.log
  fi
done
```

> 监听服务器进程

* 基于指令 `ps -ef | grep <app-name> | wc -l`

```shell
#!/bin/bash
now=`date +"%Y-%m-%d %H:%M:%S"`
for cn in docker_name1 docker_name2 docker_name3
do
  exist=`ps -ef | grep $cn | wc -l`
  if [ $exist -ne 2 ];then
    sh ./script/$cn.sh
    echo "${now} 重启进程 $cn" >> ./monitor.log
  fi
done
```

> 通过 crontab 定时执行脚本

* 创建文件编写定时规则: `vi xxx.cron`

```shell
# 每1分钟执行一次myCommand
* * * * * sh /usr/local/shell/monitor_script.sh
```

* 给脚本文件赋值可执行权限

```shell
chmod 755 /usr/local/shell/monitor_script.sh
```

* 添加到 crontab 中

```shell
crontab xxx.cron
# 也可以通过 crontab -e 直接给文件中的内容写入
# 编辑器不同保存的时候要注意
# ctrl + o 选择写入的地方默认直接回车就行了
# ctrl + x 如果没有修改内容直接退出
```

* crontab 其他相关参数

```shell
# 设定某个用户的cron服务，一般root用户在执行这个命令的时候需要此参数 
crontab -u
# 列出某个用户cron服务的详细内容
crontab -l
# 删除没个用户的cron服务
crontab -r
# 编辑某个用户的cron服务
crontab -e
# 每一分钟执行一次
* * * * * sh /usr/local/shell/java_monitor_script.sh
```

> 停止java后台服务 `api-server-0.0.1-SNAPSHOT.jar` cat api_stop.sh

```shell
#!/bin/bash
echo "Stop Procedure : api-server-0.0.1-SNAPSHOT.jar"
pid=`ps -ef |grep java|grep api-server-0.0.1-SNAPSHOT.jar|awk '{print $2}'`
echo 'old Procedure pid:'$pid
if [ -n "$pid" ]
then
kill -9 $pid
fi
```

> 启动java后台服务 `api-server-0.0.1-SNAPSHOT.jar` cat api_start.sh

```shell
echo 'Start the program : api-server-0.0.1-SNAPSHOT.jar'
chmod 777 /data/code/target/api-server-0.0.1-SNAPSHOT.jar
echo '-------Starting-------'
current_date=`date -d "-0 day" "+%Y%m%d"`
echo $current_date
cd /data/code/target/
nohup java -jar /data/code/target/api-server-0.0.1-SNAPSHOT.jar --httpPort=8081 > /data/log/api-server-0.0.1-SNAPSHOT-$current_date.log 2>&1 &
echo 'start success'
```

> jk.sh

```shell
while true
do
   pid=`ps -ef|grep demo-0.0.1-SNAPSHOT.jar|grep -v grep|wc -l`
   if [ $pid -eq 0 ];then
      echo 'Start the program : demo-0.0.1-SNAPSHOT.jar'
      chmod 777 /data/code/target/demo-0.0.1-SNAPSHOT.jar
      echo '-------Starting-------'
      cd /data/code/target/
#nohup java -jar demo-0.0.1-SNAPSHOT.jar &
      nohup java -jar /data/code/target/demo-0.0.1-SNAPSHOT.jar > /data/log/log.log 2>&1 &
      timestanp=`date '+%Y-%m-%d %H:%M:%S'`
      echo "$timestanp - restart sucess"
   else
       echo "demo is normal"
       sleep 10
   fi
done
```

> `2>&1` 标准错误输出

| 名称                 | 代码 | 操作符           | Java中表示 | Linux 下文件描述符（Debian 为例)             |
| -------------------- | ---- | ---------------- | ---------- | -------------------------------------------- |
| 标准输入(stdin)      | 0    | < 或 <<          | System.in  | /dev/stdin -> /proc/self/fd/0 -> /dev/pts/0  |
| 标准输出(stdout)     | 1    | >, >>, 1> 或 1>> | System.out | /dev/stdout -> /proc/self/fd/1 -> /dev/pts/0 |
| 标准错误输出(stderr) | 2    | 2> 或 2>>        | System.err | /dev/stderr -> /proc/self/fd/2 -> /dev/pts/0 |

[参考](https://blog.csdn.net/zhaominpro/article/details/82630528)