> ## 通用表表达式

* 通用表表达式类似程序执行中产生的变量, 简称为: CTE

```mysql
# 通过派生表
select * from (select 1) as dt

# CTE 方式
with dt as (select 1) select * from dt;

with cte(id) as (select 1) cte2(id) as (select id +1 from cte) select * from cte join cet2

# 递归 CTE 
with recursive cte(n) as (select 1 union all select n + 1 from cte where n < 20) select * from cte;
```

> 通过通用表，表达式来实现递归结构查询

```mysql
with recursive employee_paths(id,name,path) as (
	select id,name,cast(id as char(200))
    from employees
    where manager_id is null
    union all 
    select e.id, e.name, concat(ep.path, ',', e.id)
    from employee_paths as ep join employees as e on ep.id = e.manager_id
) select * from emplovee paths order by path;
```

> ## 递归限制

* 递归表达式的查询中需要包括一个终止递归的条件
  * cte_max_recursion_depth: 递归的最大次数
  * max_execution_time: sql语句最大运行时常