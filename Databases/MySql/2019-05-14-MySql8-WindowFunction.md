> ## 05-WindowFunction

* mysql 8.0 支持窗口函数（WindowFunction）, 也称分析函数。
* 窗口函数与分组聚合函数类似，但是每一行数据都生成一个结果。
* 聚合窗口函数：SUM / AVG / COUNT / MAX / MIN 等等。

```mysql
select year, country, product, profit, sum(profit) OVER(partition by country) as country_profit from sales order by country, year, product, profit;
```

> ### 大批窗口函数

```mysql
# 获取数据排名的函数
ROW_NUMBER() / RANK() / DENSE_RANK() / PERCENT_PANK()

# 获取窗口内的前几个元素和后几个元素
FIRST_VALUE()/ LAST_VALUE() / LEAD() / LAG()

# 累计分布
CUME_DIST() / NTH_VALUE() / NTILE()
```

