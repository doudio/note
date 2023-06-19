> ## ElasticSearch 数据匹配

> ### match 匹配 , match进行搜索的时候，会先进行分词拆分，拆完后，再来匹配

> ##### match 进行分词匹配

```json
{
	"query": {
		"match": {
			"field": "value"
		}
	}
}
```

> #### term 过滤 , term是代表完全匹配，也就是精确查询，搜索前不会再对搜索词进行分词拆解。

> ##### 精确匹配一个值

```json
{
	"query": {
		"term": {
			"field": "value"
		}
	}
}
```

> ##### 查询匹配多个值

```json
{
	"query": {
		"terms": {
			"field": ["value", "value"]
		}
	}
}
```

> #### range  过滤

> ##### 查询范围值

```json
{
	"query": {
	    "range": { 
			"id": { 
	        	"gte":  744718, 
	            "lt":   744728 
	        } 
	    } 
	}
}
```

| 属性  | 描述     |
| ----- | -------- |
| `gt`  | 大于     |
| `gte` | 大于等于 |
| `lt`  | 小于     |
| `lte` | 小于等于 |

> #### exists 和 missing 过滤

> ##### 查询文档中是否包含某个字段

```json
{
	"query": {
		"exists": {
			"field": "city"
		}
	}
}
```

> #### bool 查询

> 查询

```json
{
	"query": {
		"bool": {
			"must": {
				"match": {
					"city": "金"
				}
			}
		}
	}
}
```

> 不等于查询

```json
{
	"query": {
		"bool": {
			"must_not": [
				{ "term": {"city.keyword": "未知城市"} }
			]
		}
	},
	"aggs": {
		"group_by_citys": {
			"terms": { 
				"field": "city.keyword",
				"size": 777
			}
		}
	},
	"size": 0
}
```

> boot 多条件判断

```json
{
	"query": {
		"bool": {
			"must": [
				{"terms": { "qq": ["2491920818", "3183788520"] }}, 
				{"term": { "type": 0 }},
				{"term": { "city.keyword": "广州市" }}
			]
		}
	}
}
```

| 字段     | 类似    | 描述                         |
| -------- | ------- | ---------------------------- |
| must     | AND     | 查询指定文档一定要被包含。   |
| must_not | NOT AND | 查询指定文档一定不要被包含。 |
| should   | OR      | 嵌套查询只要满足一个就OK     |

> #### wildcards 查询

> 可以使用通配符进行查询

```json
{
	"query": {
		"wildcard": {
			"city": "*广*"
		}
	}
}
```

> #### regexp 查询

> 可以通过正则表达式来进行匹配

```json
{
	"query": {
		"regexp": {
			"city": "广"
		}
	}
}
```

> #### prefix 查询

> 以什么字符开头的，可以更简单地用 prefix，如下面的例子：

```json
{ 
  "query": { 
    "prefix": { 
      "city": "广" 
    } 
  } 
}
```

> #### match_phrase 查询

> 将这个查询条件作为一个词进行分词匹配，match 会将查询条件再次分词进行分词匹配

```json
{
  "query": {
    "match_phrase": {
      "city": "广"
    }
  }
}
```

> #### highlight 高亮搜索

```json
{
	"query": {
		"match": {
			"qq": 5124
		}
	}, "highlight": {
		"fields": {
			"qq": {}
		}
	}
}
```

> #### 注意：

> `match` 与 `match_phrase ` 查询的区别！
>
> * match : 先将查询条件进行分词，后以分词 去 ES 中匹配数据
> * match_phrase : 将查询条件在 ES 分词中匹配数据

> 查询不出想要的数据时可以看看该字段是否有进行分词