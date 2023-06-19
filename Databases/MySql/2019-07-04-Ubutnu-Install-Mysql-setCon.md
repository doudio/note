> ## Ubuntu 安装 MySql 并设置外链

> deb 安装 MySql

```shell
$ wget http://dev.mysql.com/get/mysql-apt-config_0.6.0-1_all.deb
$ sudo dpkg -i mysql-apt-config_0.6.0-1_all.deb
$ sudo apt-get update
$ sudo apt-get install mysql-server-5.7
$ mysql_secure_installation
```

> apt-get 安装 MySql

```shell
sudo apt-get update
sudo apt-get install mysql-server mysql-client
sudo mysql_secure_installation
```

> 1.执行以下命令分配新用户：

```sql
grant all privileges on *.* to '用户名'@'IP地址' identified by '密码';
```

>  2.执行完上述命令后用下面的命令刷新权限

```sql
flush privileges;
```

> 设置外链

> sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

> 注释：bind-address=127.0.0.1

```shell
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
# bind-address          = 127.0.0.1
```

> 重启 MySql 服务

```shell
$ sudo service mysql restart
```

