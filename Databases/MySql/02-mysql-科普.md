> [MySql官方文档地址](https://dev.mysql.com/doc/refman/8.0/en/)

* https://dev.mysql.com/doc/refman/8.0/en/

> mysql 字符相关

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