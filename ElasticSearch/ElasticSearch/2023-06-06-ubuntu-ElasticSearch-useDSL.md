> mysql 语句在线转换成 ElasticSearch 查询语句

* http://www.ischoolbar.com/EsParser/

> ElasticSearch RESTful 的请求方式介绍

| 请求方式 | 介绍       |
| -------- | ---------- |
| PUT      | 创建       |
| DELETE   | 删除       |
| PUT      | 更新或创建 |
| GET      | 查看       |

```json
# 查询索引
GET /dk
# 删除索引
DELETE /dk
# 更新或创建索引
PUT /dk
{
  "settings":{
    "number_of_shards":9,
    "number_of_replicas":1,
    "index":{
     "refresh_interval":"1s"
    }
 }, "mappings": {
   "dk_test_info": {
     "properties": {
       "hashid": {
         "type": "text",
          "fields": {
            "keyword": {
              "ignore_above": 256,
              "type": "keyword"
            }
          }
       },"username": {
         "type": "text",
          "fields": {
            "keyword": {
              "ignore_above": 256,
              "type": "keyword"
            }
          }
       }, "status": {
         "type": "text",
          "fields": {
            "keyword": {
              "ignore_above": 256,
              "type": "keyword"
            }
          }
       }, "statusList": {
            "properties": {
              "status": {
                "type": "text",
                "fields": {
                  "keyword": {
                  "ignore_above": 256,
                  "type": "keyword"
                }
              }
            }, "hashid": {
              "type": "text",
              "fields": {
                "keyword": {
                  "ignore_above": 256,
                  "type": "keyword"
                }
              }
            }, "insertTime": {
              "format": "y-MM-dd||epoch_millis",
              "type": "date"
            }
          }
        }, "insertTime": {
          "format": "y-MM-dd||epoch_millis",
          "type": "date"
        }, "updateTime": {
          "format": "y-MM-dd||epoch_millis",
          "type": "date"
        }
     }
   }
 }
}
# 添加数据
POST /dk/dk_test_info/
{
  "hashid": "AKJFLKDSAJFKSDFJ",
  "username": "zhan'shan",
  "status": "申请成功",
  "statusList": 
    {
      "status": "申请成功",
      "hashid": "JKDSFJSDKFJSIJFSFSD",
      "insertTime": "1539842093000"
    },
  "insertTime": "1539842093000",
  "updateTime": "1539842093000"
}

POST /dk/dk_test_info/
{
  "hashid": "AKJFLKDSAJFKSDFJ",
  "username": "zhan'shan",
  "status": "申请成功",
  "statusList": [
    {
      "status": "申请成功",
      "hashid": "JKDSFJSDKFJSIJFSFSD",
      "insertTime": "1539842093000"
    },
    {
      "status": "申请成功",
      "hashid": "JKDSFJSDKFJSIJFSFSD",
      "insertTime": "1539842093000"
    },
    {
      "status": "申请成功",
      "hashid": "JKDSFJSDKFJSIJFSFSD",
      "insertTime": "1539842093000"
    }
  ],
  "insertTime": "1539842093000",
  "updateTime": "1539842093000"
}

POST /dk/dk_test_info
{
  "hashid": "AKJFLKDSAJFKSDFJ",
  "username": "zhan'shan",
  "status": "申请成功",
  "statusList.status": "申请成功",
  "statusList.hashid": "JKDSFJSDKFJSIJFSFSD",
  "statusList.insertTime": "1539842093000",
  "insertTime": "1539842093000",
  "updateTime": "1539842093000"
}

# 查询数据
GET /dk/dk_test_info/_search
{
  
}

# 设置es最大数据导出(在分页过大时需要设置)
PUT /my_index/_settings
{
    "max_result_window": 2000000000
}

# 设置集群桶大小
PUT /_cluster/settings
{"persistent": {"search.max_buckets": 20000}}

# 新增字段
PUT my_index/_mapping/my_type
{
  "properties": {
    "source": {
      "type": "keyword"
    }
  }
}
```

## Elasticsearch Data Types

| **Elasticsearch type**                                       | **Elasticsearch SQL type** | **SQL type** | **SQL precision** |
| ------------------------------------------------------------ | -------------------------- | ------------ | ----------------- |
| **Core types**                                               |                            |              |                   |
| [`null`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/null-value.html) | `null`                     | NULL         | 0                 |
| [`boolean`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/boolean.html) | `boolean`                  | BOOLEAN      | 1                 |
| [`byte`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/number.html) | `byte`                     | TINYINT      | 3                 |
| [`short`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/number.html) | `short`                    | SMALLINT     | 5                 |
| [`integer`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/number.html) | `integer`                  | INTEGER      | 10                |
| [`long`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/number.html) | `long`                     | BIGINT       | 19                |
| [`double`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/number.html) | `double`                   | DOUBLE       | 15                |
| [`float`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/number.html) | `float`                    | REAL         | 7                 |
| [`half_float`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/number.html) | `half_float`               | FLOAT        | 3                 |
| [`scaled_float`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/number.html) | `scaled_float`             | DOUBLE       | 15                |
| [`keyword`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/keyword.html) | `keyword`                  | VARCHAR      | 32,766            |
| [`text`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/text.html) | `text`                     | VARCHAR      | 2,147,483,647     |
| [`binary`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/binary.html) | `binary`                   | VARBINARY    | 2,147,483,647     |
| [`date`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/date.html) | `datetime`                 | TIMESTAMP    | 29                |
| [`ip`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/ip.html) | `ip`                       | VARCHAR      | 39                |
| **Complex types**                                            |                            |              |                   |
| [`object`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/object.html) | `object`                   | STRUCT       | 0                 |
| [`nested`](https://www.elastic.co/guide/en/elasticsearch/reference/7.5/nested.html) | `nested`                   | STRUCT       | 0                 |
| **Unsupported types**                                        |                            |              |                   |
| *types not mentioned above*                                  | `unsupported`              | OTHER        | 0                 |


> 查询修改 & 查询删除语句

```json
# 查询修改
POST /my_index/_update_by_query
{
  "query": {
    "bool": {
    }
  },
  "script": {
    "source": "ctx._source.loan = params.loan",
    "params": {
      "loan": "999"
    }
  }
}
# 查询删除
GET /my_index/_delete_by_query
{
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "cityId.keyword": {
              "value": "350100"
            }
          }
        }
      ]
    }
  }
}
```