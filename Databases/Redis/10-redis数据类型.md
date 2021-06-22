> ## redis 的数据类型

> 1) String 字符串类型

```shell
# 设置key
SET runoobkey redis
OK
# 获取key
GET runoobkey
"redis"
```

* 可以同来存储用户的登录`token` : 以 token 做 key , value 做用户数据

> 2) Hash 结构

```shell
# 设置对象值
HMSET stu name "zs" age "18"
OK
# 获取对象的所有属性值
HGETALL stu
1) "name"
2) "zs"
3) "age"
4) "18"
# 获取对象中的属性
HGET stu name
"zs"
```

* hash适合在redis中存储对象结构

> 3)  List 列表

```shell
# 向list中添加元素
LPUSH runoobkey redis
(integer) 1
# 向list中添加元素
LPUSH runoobkey mongodb
(integer) 2
# 通过下标从list中获取元素 -1 代表最后一位
LRANGE runoobkey 0 -1
1) "mongodb"
2) "redis"
# 在指定的val前新增元素
linsert runoobkey before redis mysql
(integer) 3
# 再次查询list中的元素
LRANGE runoobkey 0 -1
1) "mongodb"
2) "mysql"
3) "redis"
```

* 可以用来做排行榜相关的业务

> 4) Set 集合

```shell
# 向set集合中添加元素
SADD set a b c d
(integer) 4
# 获取set集合中的值
SMEMBERS set
1) "d"
2) "b"
3) "c"
4) "a"
# 获取两个集合的交集
sinter set set1
1) "d"
2) "b"
3) "c"
4) "a"
# 获取两个集合的差值
sdiff set set1
(empty list or set)
# 获取两个集合的差值
sdiff set1 set
1) "e"
2) "f"
3) "g"
# 获取两个集合的并集
sunion set1 set
1) "e"
2) "b"
3) "a"
4) "d"
5) "f"
6) "c"
7) "g"
```

* set集合是无序的且元素不能重复
* 支持多个集合间的交集、并集、差集操作。可以利用这些集合操作，解决程序开发过程当中很多数据集合间的问题。

> 5) sorted set 有序的 set

```shell
# 向zset中添加元素 语法 ZADD <key> <用于排序的double> <值>
ZADD set 1 a
(integer) 1
# 向zset中添加元素 语法 ZADD <key> <用于排序的double> <值>
ZADD set 2 b
(integer) 1
# 获取set中的值和 score
ZRANGE set 0 -1 WITHSCORES
1) "a"
2) "2"
3) "b"
4) "2"
# 获取set中的值
ZRANGE set 0 -1
1) "a"
2) "b"
```

* zset 是一个有序的且不重复的结构
* 不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。
* 有序集合的成员是唯一的,但分数(score)却可以重复。

> HyperLogLog

```shell
# 向hyperLogLog中添加元素
PFADD runoobkey "redis"
(integer) 1
PFADD runoobkey "mysql"
(integer) 1
PFADD runoobkey "mongodb"
(integer) 1
# 统计元素基数个数
PFCOUNT runoobkey
(integer) 3
# 将多个 HyperLogLog 合并为一个 HyperLogLog
PFMERGE newSource source1 source2
```

* Redis HyperLogLog 是用来做基数统计的算法 :: 基数(不重复元素)
* 可以用来做大数据的统计，不大不要用

