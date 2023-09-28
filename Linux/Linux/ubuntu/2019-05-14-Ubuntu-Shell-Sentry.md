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
```

