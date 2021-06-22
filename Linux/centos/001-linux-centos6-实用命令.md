> ## linux 实用命令

```shell
tail -f logs/catalina.2019-01-08.log
```

> `tail -f` `日志文件`

> 后台启动程序

```shell
nohup java -jar XXX.jar &
```

> 检查 IP 与端口是否通

```shell
telnet localhost 22
```

> 两台 linux 之间拷贝文件

```shell
#将文件/文件夹`从远程拷至本地`(下载文件)
scp -r <root>@<ip>:<ip-path> .

#将文件/文件夹`从本地拷至远程`(发送文件)
scp -r <local-path> <root>@<ip>:<ip-path>
```

> 磁盘相关

```shell
# 查看有哪些盘和使用情况
df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        50G   12G   36G  25% /
devtmpfs        7.8G     0  7.8G   0% /dev
tmpfs           7.8G   24K  7.8G   1% /dev/shm
tmpfs           7.8G  756K  7.8G   1% /run
tmpfs           7.8G     0  7.8G   0% /sys/fs/cgroup
tmpfs           1.6G     0  1.6G   0% /run/user/0
/dev/vdb1       493G   19G  449G   4% /bigdata

# 查询当前目录下的文件与文件夹的占用量
du -sh ./*
684K    ./sys
18M     ./VM_16_11_centos.log
4.0K    ./VM_16_11_centos-slow.log
```

> 给某个目录赋给莫个用户

```shell
chown -R esuser:esuser /data
```

> 给某个文件赋予权限

```shell
# 给某个文件赋予最高权限
chmod 777 demo.txt

chmod 755 demo.txt
#第一个数字7是档案拥有者的权限(可读+可写+可执行=4+2+1)
#第二个数字5是群组的权限(可读+可执行=4+1)
#第三个数字5是其他人的权限(可读+可执行=4+1)
```

> 创建超链接, 编辑内容可同步

```shell
ln -s /etc/nginx/conf.d/doudio.conf ./nginx/doudio.conf
```