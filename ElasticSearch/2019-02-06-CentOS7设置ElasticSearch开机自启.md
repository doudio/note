> ## CentOS7 设置 ElasticSearch 开机自启

> 查看当前开机自启的服务

```shell
$ chkconfig --list

Note: This output shows SysV services only and does not include native
      systemd services. SysV configuration data might be overridden by native
      systemd configuration.

      If you want to list systemd services use 'systemctl list-unit-files'.
      To see services enabled on particular target use
      'systemctl list-dependencies [target]'.

netconsole      0:off   1:off   2:off   3:off   4:off   5:off   6:off
network         0:off   1:off   2:on    3:on    4:on    5:on    6:off
```

> 编写 ElasticSearch 系统启动服务文件

```shell
cd /etc/init.d
vi elasticsearch

----
#!/bin/bash
#chkconfig: 345 63 37
#description: elasticsearch
#processname: elasticsearch-5.4.0

export ES_HOME=/usr/local/es/eshome/es540     【这个目录是你Es所在文件夹的目录】

case $1 in
        start)
                su es<<!        【es 这个是启动es的账户，如果你的不是这个记得调整】
                cd $ES_HOME
                ./bin/elasticsearch -d -p pid
                exit
!
                echo "elasticsearch is started"
                ;;
        stop)
                pid=`cat $ES_HOME/pid`
                kill -9 $pid
                echo "elasticsearch is stopped"
                ;;
        restart)
                pid=`cat $ES_HOME/pid`
                kill -9 $pid
                echo "elasticsearch is stopped"
                sleep 1
                su es<<!     【es 这个是启动es的账户，如果你的不是这个记得调整】
                cd $ES_HOME
                ./bin/elasticsearch -d -p pid
                exit
!
                echo "elasticsearch is started"
        ;;
    *)
        echo "start|stop|restart"
        ;;
esac
exit 0
----
```

> 修改文件权限

```shell
chmod 777 elasticsearch
```

> 添加和删除服务并设置启动方式

```shell
chkconfig --add elasticsearch　　　　【添加系统服务】
chkconfig --del elasticsearch　　　　【删除系统服务】
```

> 关闭和启动服务

```shell
service elasticsearch start　　　　　【启动】
service elasticsearch stop　　　　　 【停止】
service elasticsearch restart　　   【重启】
```

> 设置服务是否开机启动

```shell
chkconfig elasticsearch on　　　　　　【开启】
chkconfig elasticsearch off　　   　 【关闭】
```

* [原文地址](https://www.cnblogs.com/Rawls/p/10937280.html):: https://www.cnblogs.com/Rawls/p/10937280.html