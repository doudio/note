> Ubuntu 设置 root 密码

```shell
sudo passwd root
```

> Ubuntu 设置 root 远程登陆

```shell
vim /etc/ssh/sshd_config

## 添加配置
PermitRootLogin yes
##

# 重启服务器
reboot
```

> 添加 java 环境配置

```shell
export JAVA_HOME=/usr/local/apps/java/jdk-12.0.2
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```