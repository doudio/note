> 影响数据库的因素

* sql查询速度
* 服务器硬件
* 网卡流量
* 磁盘IO

> 风险

* 效率低下的sql

* mysql 不支持多 cpu 并发运算
* 大量的并发, 数据库连接数被占满
* 高频的cpu使用率, 因cpu资源耗尽而出现的宕机
* 磁盘io性能突然下降
  * 使用更快的磁盘设备
  * 其它大量消耗磁盘性能的计划任务 , 调整计划任务 , 做好磁盘维护
* 网卡io被占满 (1000mb/8~100mb)
  * 如何避免无法连接数据库的情况
  * 减少从服务器的数量
  * 进行分级缓存
  * 避免使用 `select *` 进行查询, 查询出没有必要的列也会耗费网络流量
  * 分离业务网络和服务器网络

> 还有什么会影响数据库性能

* 大表给我们带来的问题
  * 什么样的表可以称之为大表
    * 记录行数巨大 , 单表超过千万行
    * 表数据文件巨大 , 表数据文件超过 10g
  * 大表对查询的影响
    * 慢查询 : 很难在一定的时间内过滤出所需要的数据, 例如: `order_from 订单来源`
  * 大表对DDL操作的影响
    * 会造成长时间的主从延迟
    * 影响正常的数据操作
  * 分库分表吧一张大表分成多个小表
    * 分表主键的选择
    * 分表后跨分区数据的查询和统计
  * 大表的历史数据归档, 减少对前端业务的影响
* 大事务给我们带来的影响
  * 定义 : 运行时间比较长 , 操作的数据比较多的事务
    * 锁定太多的数据 , 造成大量的阻塞和锁超时
    * 回滚时所需时间较长
    * 执行时间长, 容易造成主从延迟
  * 如何处理大事务
    * 避免一次处理太多的数据
    * 移除不必要在事务中的 select 操作

> pt工具介绍

​	Percona Toolkit简称pt工具，是Percona公司开发用于管理[MySQL](https://cloud.tencent.com/product/cdb?from=10680)的工具，功能包括检查主从复制的数据一致性、检查重复索引、定位IO占用高的表文件、在线DDL等，DBA熟悉掌握后将极大提高工作效率。

> centos 安装

```shell
# 下载文件地址 https://www.percona.com/downloads/percona-toolkit/LATEST/
# 下载rpm包
wget https://downloads.percona.com/downloads/percona-toolkit/3.3.1/binary/redhat/7/x86_64/percona-toolkit-3.3.1-1.el7.x86_64.rpm
# 安装rpm
sudo yum localinstall percona-toolkit-3.3.1-1.el7.x86_64.rpm
# 检测版本, 看看是否装成功
pt-table-checksum --version 
out >> pt-table-checksum 3.3.1
```

> 工具集介绍

| 类型   | 指令                     | 作用                             |
| ------ | ------------------------ | -------------------------------- |
| 开发类 | pt-duplicate-key-checker | 列出并删除重复的索引和外键       |
|        | pt-online-schema-change  | 在线修改表结构                   |
|        | pt-show-grants           | 规范化和打印权限                 |
|        | pt-upgrade               | 仔多服务器上执行查询, 并比较不同 |
| ---    | ---                      | ---                              |
| 性能类 | pt-index-usage           | 分析日志中索引使用情况, 并出报告 |
|        | pt-pmp                   | 为查询结果跟踪, 并汇总跟踪结果   |
|        | pt-visual-explain        | 格式化执行计划                   |
|        | pt-table-usage           | 分析日志中查询并分析表使用情况   |
| ---    | ---                      | ---                              |
| 配置类 | pt-config-diff           | 比较配置文件和参数               |
|        | pt-mysql-summary         | 对mysql配置和status进行汇总      |
|        | pt-variable-advisor      | 分析参数, 并提出建议             |
| ---    | ---                      | ---                              |
| 监控类 | pt-deadlock-logger       | 提取和记录mysql死锁信息          |
|        | pt-fk-error-logger       | 提取和记录外键信息               |
|        | pt-mext                  | 查看status样本信息               |
|        | pt-query-digest          | 分析日志并生成报告               |
| ---    | ---                      | ---                              |
| 复制类 | pt-heartbeat             | 监控mysql复制延迟                |
|        | pt-slave-delay           | 设定从落后主的时间               |
|        | pt-slave-find            | 查找和打印所有mysql复制层级关系  |
|        | pt-slave-restart         | 监控salve错误, 并尝试重启salve   |
|        | pt-table-checksum        | 校验主从复制一致性               |
|        | pt-table-sync            | 高效同步表数据                   |
| ---    | ---                      | ---                              |
| 系统类 | pt-diskstats             | 查看系统磁盘状态                 |
|        | pt-fifo-split            | 模拟切割文件并输出               |
|        | pt-summary               | 收集显示系统概况                 |
|        | pt-stalk                 | 出现问题时, 收集诊断数据         |
|        | pt-sift                  | 浏览由 pt-sift 创建的文件        |
|        | pt-ioprofile             | 查询进程IO并打印一个IO活动表     |
| ---    | ---                      | ---                              |
| 实用类 | pt-archiver              | 将数据归档到另一个表或文件中     |
|        | pt-find                  | 查询并执行指令                   |
|        | pt-kill                  | 干掉符合条件的sql                |
|        | pt-fingerprint           | 查询转密文                       |
|        | pt-align                 | 对齐结构输出                     |

> 语法

```shell
# pt-duplicate-key-checker --host=localhost --port=3306 --user=<user> --password=<pass> --database=<db> --tables=<tbl>
# 1.1 列出并删除重复的索引和外键
pt-duplicate-key-checker h=localhost,P=3306,u=root,p=root@2021,D=test --tables=my_tab

# 2.1在线添加字段,不锁表
# 语法可百度 >> https://blog.51cto.com/azhuang/1588019
pt-online-schema-change -uroot -hlocalhost -p123 -S /tmp/mysql.sock --charset utf8  --alter='add column name char(4)' --execute D=test,t=user
```

* (工具分类) https://cloud.tencent.com/developer/article/1533505
* (语法) https://blog.51cto.com/azhuang/1588019

> 获取sql在每一步耗费的时间

* show profile 方式
* 参考: https://www.cnblogs.com/xiaoboluo768/p/5157909.html

```shell
# 查看是否支持这个功能（查询为yes表示支持）：
mysql > show variables like 'have_profiling';
# 需要临时使用时直接sql命令行中输入：set profiling=1;来开启
mysql> set profiling=1;
# 执行查询语句
mysql> select count(*) from xx;
# 然后再执行：show prifiles;命令，所有的查询SQL都会被列出来
mysql> show profiles;
# 然后根据编号查询具体SQL的执行过程，这里演示只执行了一句，那就选项query id为1
# 语法: show profile for query <Query_ID>;
mysql> show profile for query 1;

# 当查到最耗时的线程状态时，可以进一步选择all或者cpu,block io,page faults等明细类型来查看mysql在每个线程状态中使用什么资源上耗费了过高的时间：
mysql> show profile cpu for query 2;

# 测试完毕后，关闭参数：
mysql> set profiling=0
```

* performance_schema 库
* 参考: https://blog.csdn.net/sinat_42483341/article/details/106908586