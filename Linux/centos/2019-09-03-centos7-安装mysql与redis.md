> #### CentOS 安装mysql与redis

> 安装MySql

```shell
# 从mysql官网下载rpm安装包
wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
# 安装mysql源
yum localinstall mysql57-community-release-el7-8.noarch.rpm
# 检查是否有mysql源
yum repolist enabled | grep "mysql.*-community.*"
# 安装mysql
yum install mysql-community-server
# 启动mysql服务
systemctl start mysqld
# 设置开机自启
systemctl enable mysqld
systemctl daemon-reload
# 查看mysql初始化默认密码
cat /var/log/mysqld.log | grep root@localhost: 
# localhost连接上mysql后修改默认密码
set password for 'root'@'localhost' = password('pass');
```

> 安装 redis

```shell
yum install redis
# 查找redis配置文件
whereis redis.config
# 启动
systemctl start redis.service
# 重启
systemctl restart redis.service
# 关闭
systemctl stop redis.service
# 添加开机自启
systemctl enable redis
```
