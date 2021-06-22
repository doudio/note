> ## 优化器索引

* 隐藏索引
* 降序索引
* 函数索引

> ## 隐藏索引

* mysql 8.0 开始支持隐藏索引(invisible index), 不可见索引
* 隐藏索引不会背优化器使用，但是任然需要进行维护
* 应用场景
  * 软删除
  * 灰度发布

```mysql
# 创建一个隐藏索引
create index j_idx on t1(j) invisible;

# 查看表中的索引
show index from t1\G
*************************** 1. row ***************************
        Table: t1
   Non_unique: 1
     Key_name: i_idx
 Seq_in_index: 1
  Column_name: i
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: BTREE
      Comment:
Index_comment:
      Visible: YES # 当前索引可见
   Expression: NULL
*************************** 2. row ***************************
        Table: t1
   Non_unique: 1
     Key_name: j_idx
 Seq_in_index: 1
  Column_name: j
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: BTREE
      Comment:
Index_comment:
      Visible: NO # 当前索引不可见
   Expression: NULL
2 rows in set (0.00 sec)
```

* 通过`explain`可以看到在根据不可见索引列查询时是不使用索引的

```mysql
# 查看查询优化器的参数
select @@optimizer_switch\G
*************************** 1. row ***************************
@@optimizer_switch: index_merge=on,index_merge_union=on,index_merge_sort_union=on,index_merge_intersection=on,engine_condition_pushdown=on,index_condition_pushdown=on,mrr=on,mrr_cost_based=on,block_nested_loop=on,batched_key_access=off,materialization=on,semijoin=on,loosescan=on,firstmatch=on,duplicateweedout=on,subquery_materialization_cost_based=on,use_index_extensions=on,condition_fanout_filter=on,derived_merge=on,use_invisible_indexes=off,skip_scan=on

# 在当前会话中修改参数
set session optimizer_switch="use_invisible_indexes=on";

# 设置索引可见
alter table t1 alter index j_idx visible;
# 设置索引不可见
alter table t1 alter index j_idx invisible;
```

* 主键是不可以设置不可见的

> ## 降序索引

* mysql8.0 开始真正的支持降序索引
* 只有 InnoDB 存储引擎支持降序索引, 只支持 BTREE 降序索引
* mysql8.0 不再对 GROUP BY 操作进行隐式排序

```mysql
create table t2(c1 int, c2 int, index idx(c1 asc, desc));
```

> ## 函数索引

* mysql 8.0.13 开始支持在索引中使用函数(表达是)的值
* 支持降序索引, 支持 JSON 数据的索引
* 函数索引基于虚拟列功能实现

```mysql
# 创建一个函数索引
create index func_idx on t3( (UPPER(lie2)) );
```

