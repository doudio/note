> [MySql官方文档地址](https://dev.mysql.com/doc/refman/8.0/en/)

* https://dev.mysql.com/doc/refman/8.0/en/

---

* ### mysql 字符相关

* utf8mb4 与 utf8 的区别

```mysql
MySQL在5.5.3版本以后增加了utf8mb4编码，其中mb4是most bytes 4的含义，用来兼容四个字节的Unicode（万国码）。utf8mb4是utf8的一个扩展。
```

* Mysql 数据库排序规则

1. 两个不同的字符集不能有相同的排序规则
2. 两个字符集有一个默认的排序规则
3. 有一些常用的命名规则。如_ci结尾表示大小写不敏感（caseinsensitive）,_cs表示大小写敏感（case sensitive）,_bin表示二进制的比较（binary）。
4. utf-8有默认的排序规则：SHOW CHARSET LIKE 'utf8%';

> 区别

```mysql
utf8_general_ci 不区分大小写，这个你在注册用户名和邮箱的时候就要使用。
utf8_general_cs 区分大小写，如果用户名和邮箱用这个 就会照成不良后果。
utf8_bin:字符串每个字符串用二进制数据编译存储。 区分大小写，而且可以存二进制的内容。

utf8_general_ci校对速度快，但准确度稍差。
utf8_unicode_ci准确度高，但校对速度稍慢。

utf8mb4_general_ci
```

* ### 索引条件下推 -- Mysql5.7 新特性

* https://blog.csdn.net/Artisan_w/article/details/108077872

  索引条件下推（ICP，Index Condition Pushdown），ICP是Mysql针对索引从表中检索时的一种优化特性，在没有ICP的特性如下：

  1. 根据索引读取一条索引记录，然后使用索引的叶子节点中的主键值回表读取整个表行。
  2. 判断这行记录十一否符合where条件

  有ICP处理后:

  	1. 根据索引读取一条索引记录，但并不回表取出整行数据
  	2. 判断记录是否满足where条件的一部分，并且只能使用索引字段进行检查。如果不满足条件则继续获取下一条索引记录。
  	3. 如果满足条件则返回整行回表信息。
  	4. 再判断where的剩余部分，选择满足条件的记录

  

  ICP的意思就是在筛选字段在索引中的where条件从服务器层下推到存储引擎层，这样可以在存储引擎层过滤数据。

  由此可见ICP可以减少存储引擎访问基表的次数和服务器访问存储引擎的次数。

ICP的限制使用如下：

1. 只能用于InnoDB和MyISAM
2. 适用于range，ref，eq_ref和ref_or_null访问方式
3. 适用于二级索引
4. 不适用于虚拟字段的二级索引


> 统计区间

```sql
SELECT
        elt(INTERVAL ( duration, 0, 10, 20, 30, 40, 50, 60 ),
                '0-10秒',
                '11-20秒',
                '21-30秒',
                '31-40秒',
                '41-50秒',
                '51-60秒',
                '大于60秒' 
        ) AS callDuration,
        count(*) AS amount 
FROM
        phone 
WHERE
        g_id =101 and create_time BETWEEN '2023-02-06  00:00:00' 
        AND '2023-02-06  23:59:59' 
        AND status_name = '已接听' 
        GROUP BY callDuration;
```