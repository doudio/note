> ## ElasticSearch 数据统计

> #### 获取[ 最大 | 最小 | 平均 | 求和 ]

```json
{
  "query": {
    "term": {
      "customQq": "512490163"
    }
  },
  "aggs": {
    "max_age": {
      "<max|min|avg|sum>": {
        "field": "id"
      }
    }
  }
}
```

> `"max_age": {` 这里只是一个名字，方便自己取值。

> #### 分组查询

```json
{
  "query": {
  	"term": {
  		"fraudType": 0
  	}
  }, "aggs": {
  	"group_by_city": {
  		"terms": {"field": "city.keyword"}
  	}
  }
}
```

> #### 查询排序

```json
{
	"query": {
		"bool": {
			"must": [ 
				{"term": { "tyep": 0 }}
			]
		}
	}, "sort": [
		{ "insertTime": { "order": "asc" }}
	]
}
```

