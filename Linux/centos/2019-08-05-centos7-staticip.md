# centos7 static ip

#### 修改网络配置

```shell
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

##### 修改文件中的配置项

```properties
ONBOOT=no	改成	ONBOOT=yes
BOOTPROTO=dhcp	改成	BOOTPROTO=static
添加：
	IPADDR=192.168.249.88
	NETMASK=255.255.255.0
	PREFIX=24
	GATEWAY=192.168.249.2
	DNS1=114.114.114.114
```

如：

```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=3f158a2d-55ec-4e13-be2f-98b5bcf939da
DEVICE=ens33
ONBOOT=yes

IPADDR=192.168.249.88
NETMASK=255.255.255.0
GATEWAY=192.168.249.2
DNS1=114.114.114.114
PREFIX=24
```

#### 在wins中的 VMnet8 中修改成同一网段

如：

```properties
IP 地址：192.168.246.36
子	网：255.255.255.0
默认网关：192.168.249.2
```

