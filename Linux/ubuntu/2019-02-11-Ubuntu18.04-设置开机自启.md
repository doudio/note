> ## Ubuntu18.04 设置开机自启

> 准备好需要开机自启的 shell `sudo vim /etc/rebootRun.sh`

```shell
#!/bin/sh -e
#
# Script description
# 
echo "Script Run Success" > /usr/local/testrun.log
exit 0
```

* -e 的含义

  * 每条指令之后，都可以用$？去判断它的返回值，零就是正确执行，非零就是执行有误
  * 加了-e之后，就不用自己写代码去判断返回值，返回非零，脚本就会自动退出。

* #### 需要注意 在 `Ubuntu16.10` 开始不再使用 `/etc/init.d/`

> 给 shell 设置权限

```shell
sudo chmod +x /etc/rebootRun.sh
```

> 编写 service 文件 `sudo vim /etc/systemd/system/rc-rebootRun.service`

```shell
[Unit]
Description=call rebootRun.sh
ConditionPathExists=/etc/rebootRun.sh

[Service]
Type=forking
ExecStart=/etc/rebootRun.sh start
TimeoutSec=0    
StandardOutput=tty
RemainAfterExit=yes
SysVStartPriority=99

[Install]
WantedBy=multi-user.target
```

* `[Unit]` : 启动顺序与依赖关系
* `[Service]` : 启动行为,如何启动，启动类型
* `[Install]` : 定义如何安装这个配置文件，即怎样做到开机启动

> 通过 systemctl 给 shell 设置为服务

```shell
#  启用这个一个 service 
sudo systemctl enable rc-rebootRun
# 启动服务
sudo systemctl start rc-rebootRun.service
# 查看服务状态
sudo systemctl status rc-rebootRun.service
```

