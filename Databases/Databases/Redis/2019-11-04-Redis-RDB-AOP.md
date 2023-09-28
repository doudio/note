> ### Redis RDB AOP

> > #### RDB 存储
>
> > 优势
> >
> > 1. 适合大规模的数据恢复
> > 2. 对数据完整性和一致性要求不高
> >
> > 劣势
> >
> > 1. 在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改
> > 2. fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
>
> RDB方式要比AOF方式更加的高效。
>
> RDB的缺点是最后一次持久化后的数据可能丢失。

```
################################ SNAPSHOTTING  ################################
#
# Save the DB on disk:
#
#   save <seconds> <changes>
#
#   Will save the DB if both the given number of seconds and the given
#   number of write operations against the DB occurred.
#
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
#
#   Note: you can disable saving completely by commenting out all "save" lines.
#
#   It is also possible to remove all the previously configured save
#   points by adding a save directive with a single empty string argument
#   like in the following example:
#
#   save ""

save 900 1
save 300 10
save 60 10000

dbfilename dump.rdb

dir ./
```

> `save` `<seconds>` `<changes>`
>
> >  `<seconds>` :: 在多少秒内
>
> >  `<changes>` :: 执行多少次写操作
>
> 满足条件进行一次数据快照

---

> > ### AOP 存储
>
> > 优势
> >
> > 1. 每修改同步：appendfsync always   同步持久化 每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好
> > 2. 每秒同步：appendfsync everysec    异步操作，每秒记录   如果一秒内宕机，有数据丢失
> > 3. 不同步：appendfsync no   从不同步
> >
> > 劣势
> >
> > 1. 相同数据集的数据而言aof文件要远大于rdb文件，恢复速度慢于rdb
> > 2. aof运行效率要慢于rdb,每秒同步策略效率较好，不同步效率和rdb相同
>
> AOP 备份 规则将所有写的操作记录下来达到备份的效果

```
############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check http://redis.io/topics/persistence for more information.

appendonly no

# The name of the append only file (default: "appendonly.aof")

appendfilename "appendonly.aof"

# 同步策略
# 每修改一次同步到磁盘
# appendfsync always

# 每秒同步
appendfsync everysec

# 不同步
# appendfsync no
```

> 开启 AOP 存储需要将: appendonly no 改为 appendonly yes

---

> #### 修复备份数据( *.aof | *.rdb )

> 使用 `Redis` 工具 : `redis-check-aof` `redis-check-rdb`
>
> 语法: `redis-check-aof` `--fix` `appendonly.aof`

