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

# 查看有几张CD光驱连接
ll /dev | grep cdrom

# /dev/sr0 是光驱的设备，/dev/cdrom 代表光驱cdrom 是 sr0 的软链接.
# ll /dev/cdrom 和 ll /dev/sr0 看看显示
# 设备的设备文件名，其中的“sr”代表“SCSI CD-ROM”，数字“0”表示设备的编号。
# 在Linux系统中，光驱设备通常被称为“sr”，而硬盘设备则被称为“sd”。
# 因此，/dev/sr0代表系统中的第一个光驱设备。

# 挂载 光驱
sudo mount /dev/cdrom /mnt（可自定义目录）

# 卸载（弹出）光驱设备
sudo umount /mnt
```

* 引用
* [Linux系统中的设备文件——/dev/sr0](https://m.163.com/dy/article/IIPC7VCO0556471K.html)

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

alias logstash="/data/soft/logstash-6.8.2/bin/logstash"
```

> 设置时区（CentOS 7）

```shell
# 先执行命令timedatectl status|grep 'Time zone'查看当前时区，如果不是中国时区（Asia/Shanghai），则需要先设置为中国时区，否则时区不同会存在时差。
# 已经是Asia/Shanghai，则无需设置
[root@xiaoz shadowsocks]# timedatectl status|grep 'Time zone'
       Time zone: Asia/Shanghai (CST, +0800)

#执行下面的命令设置时区
#设置硬件时钟调整为与本地时钟一致
timedatectl set-local-rtc 1
#设置时区为上海
timedatectl set-timezone Asia/Shanghai
```

> centos 通过指令同步日期

```shell
# 输出当前时间
date "+%Y-%m-%d %H:%M:%S"
yum -y install ntpdate
ntpdate -u pool.ntp.org
#中国, cn.ntp.org.cn
#中国香港, hk.ntp.org.cn
#美国, us.ntp.org.cn
```

* 同步时间后可能部分服务器过一段时间又会出现偏差，因此最好设置crontab来定时同步时间，方法如下：

```shell
#安装crontab
yum -y install crontab
#创建crontab任务
crontab -e
#添加定时任务
*/20 * * * * /usr/sbin/ntpdate pool.ntp.org > /dev/null 2>&1
#重启crontab
service crond reload
```

`@see` https://www.xiaoz.me/archives/12989

> 线上Java 高CPU占用、高内存占用排查思路

`@see` https://blog.csdn.net/baiye_xing/article/details/90483169

> Java线程快照查看

```shell
#查看CPU占用最高的Java进程
top -c

一些常见的 top 指令参数如下：

    -h：显示帮助信息。
    -c：显示完整的命令行，而不只是进程名。
    -d：设置刷新间隔，单位为秒。
    -o：按照指定的字段排序，如 CPU、MEM、PID 等。
    -u：只显示指定用户的进程。
    -p：只显示指定 PID 的进程。

htop是一个类似于top命令的Linux进程监控工具，它提供了一个交互式的界面

要根据内存或者CPU排序在运行top指令后

* 按M可改变内存使用率的输出结果
* 按Shift+M来根据内存排序
* 按Shift+P来根据CPU排序

# 查询内存
free -h

#查看进程中线程使用情况
top -H -p <pid>

#将线程id转为16进制
printf "%x\n" <pid>

#生成线程快照日志
jstack <pid> > <pid>.log

#导出Java应用程序的内存快照文件
jmap -dump:format=b,file=<pid>.hprof <pid>

jmap -histo <pid> | more

>> 关于抓java的dump中live参数
我们经常需要查看内存中的一些变量的值，来定位生产环境的问题。一般会使用jmap来抓dump，在抓dump的时候，我们会把堆全部扒下来：
jmap -dump:format=b,file=path pid
然后会生成一个几百M的包，让运维人员从生产环境拖下来再传给你，然后你用jvisualvm打开，等你打开这个dump的时候，看到你想看的内存的时候，基本上半天时间已经过去了。
其实我们丢了一个很重要的参数：live,这个参数表示我们需要抓取目前在生命周期内的内存对象，也就是说GC收不走的对象，然后我们绝大部分情况下，需要的看的就是这些内存。如果我们把这个参数加上：
jmap -dump:live,format=b,file=path pid
那么抓下来的dump会减少一个数量级，在几十M左右，这样我们传输，打开这个dump的时间将大大减少，为解决故障赢取了宝贵的时间。
```

> .hprof 内存分析工具
* https://help.aliyun.com/document_detail/32024.html#section-wue-8jl-ygt
* https://www.eclipse.org/mat/downloads.php
* 文件太大改 `MemoryAnalyzer.ini` -Xmx9024m

> yum -y install epel-release 是一个Linux下的命令，用于在系统上安装epel-release软件包。

epel-release是一个由Fedora社区维护的软件包仓库。它提供了许多常用的、不在官方软件源中的软件包，如PHPMyAdmin、Node.js、Git等。通过安装epel-release软件包，可以将epel软件仓库添加到系统的软件源中，从而使得系统可以使用epel软件仓库中的软件包。
