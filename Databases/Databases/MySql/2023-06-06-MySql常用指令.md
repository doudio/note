> ## 给 MySql 添加外链指令

> 1.执行以下命令分配新用户：

```sql
grant all privileges on *.* to '用户名'@'IP地址' identified by '密码';
# 分开2条语句先创建用户 再给用户赋所有权限
CREATE USER '用户名'@'IP地址' IDENTIFIED BY '密码';
GRANT ALL ON *.* TO '用户名'@'IP地址';

# 创建账号并赋值指定权限
CREATE USER 'test'@'localhost' IDENTIFIED BY 'pass';
GRANT select ON *.* TO 'test'@'localhost';

# 合并成一条语句:模板
grant [all|select|insert|update|delete] on <database>.<table> to <user>@<localhost> identified by 'pass';
# 合并成一条语句:demo
grant select,insert,update,delete on *.* to 'test'@'127.0.0.1' identified by '123456';

# mysql8 语法
alter user 'root'@'%' identified with mysql_native_password by '123456';

# 删除用户
drop user root@'%';
```

>  2.执行完上述命令后用下面的命令刷新权限

```sql
flush privileges;

# 查看mysql默认密码
cat /var/log/mysqld.log
cat /var/log/mysqld.log | grep root@localhost:
```

> 3.重启mysql服务

> #### Ubuntu

```shell
启动mysql：
方式一：sudo /etc/init.d/mysql start 
方式二：sudo service mysql start

停止mysql：
方式一：sudo /etc/init.d/mysql stop 
方式二：sudo service mysql stop

重启mysql：
方式一：sudo/etc/init.d/mysql restart
方式二：sudo service mysql restart
```

> #### 备份 与 恢复 MySql 数据库

> 备份数据库：`mysqldump` `-u` `用户名` `-p` `数据库名` `<数据表::不指定就是全部>` `>` `sql文件`

```shell
root@server:~/mysql$ mysqldump -u root -p database > database.sql
Enter password: 
```

> 恢复数据库：`mysql` `-u` `用户名` `-p` `<` `sql文件`

```shell
root@server:~/mysql$ mysql -u root -p < database.sql
Enter password: 
```

> 如果在恢复数据库时出现问题查看：database.sql 文件中是否有选择数据库

```shell
# 如果没有选择数据库就 vim database.sql
# 在 sql 前面添加
CREATE DATABASE `db_name`;
USE `db_name`;
```

> 将 MySql 查询结果导出外部文件

```shell
# 有权限导出哪个目录
show variables like '%secure%';
# 将查询结果导出外部文件
select * from table into outfile '/tmp/data.txt';
# 如果需要指定分隔符在指令后追加 fields terminated by '|'
```

| 值                     | 描述                  |
| ---------------------- | --------------------- |
| secure_file_prive=null | 限制mysqld 不允许导入 |
| secure_file_priv=/tmp/ | 限制mysqld 的导入     |
| secure_file_priv       | 不对mysqld 的导入     |
| secure_file_priv="/"   | 可以导出至任意目录    |


> #### mysql 在 (**centos|ubuntu**) 下开启日志

> mysql会话中查询系统变量的语法

```mysql
SHOW VARIABLES LIKE "general_log%"; 
#启用日志::注意这样设置重启mysql后会重置
SET GLOBAL general_log = 'ON';
```

> **ubuntu**

```properties
# 查看配置文件目录
cat /etc/mysql/my.cnf
# 编辑mysql配置文件
vim /etc/mysql/mysql.conf.d/mysqld.cnf

--- 修改内容如下
# 解开如下注解开启: 二进制日志
# server-id = 二进制日志的开关
# log_bin = 二进制日志的目录
server-id               = 1
log_bin                 = /var/log/mysql/mysql-bin.log

# 解开如下注解开启: 开启查询日志
# general_log_file = 查询日志的文件
# general_log = 查询日志的开关
# log_timestamps = 设置时间
general_log_file        = /var/log/mysql/mysql.log
general_log             = 1
log_timestamps = SYSTEM

# 解开如下注解开启: 开启慢日志
# slow_query_log = 慢查询日志开关
# slow_query_log_file = 慢查询日志文件
# long_query_time = 慢查询时间默认单位秒
# log-queries-not-using-indexes = 记录没有使用上索引的sql
slow_query_log          = 1
slow_query_log_file     = /var/log/mysql/mysql-slow.log
long_query_time = 2
log-queries-not-using-indexes = on
---

# ubuntu 重启mysql
service mysql restart
```

> **centos**

```properties
# 编辑默认配置文件
vim /etc/my.cnf

--- 添加如下配置
general_log=1
general_log_file=/var/lib/mysql/VM_16_11_centos.log
# 指定自动清理binlog日志的天数
expire_logs_days=7
slow_query_log=1
slow_query_log_file=/var/lib/mysql/VM_16_11_centos-slow.log
---

# 重启mysql服务
systemctl restart mysqld
```

* 注意: 配置的日志最好在mysql的默认目录下, mysql写出日志需要有文件权限

> 根据一个字段去重 this is incompatible with sql_mode=only_full_group_by

```
select * from user;

---
uname age gender
张三  10  1
李四  11  0
张三  11  1
---

# 根据uname去重但是还需要保留后面的字段, 需要设置一个参数该参数只对当前会话有效
# 如果不设置直接group by会出现 with sql_mode=only_full_group_by
SET SESSION sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY,',''));
select uname, age, gender from user group by uname;
```