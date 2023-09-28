> ## ElasticSearch-dump 数据迁移工具

> #### ElasticSearch-dump 基于 nodejs 先安装 npm

* https://github.com/elasticsearch-dump/elasticsearch-dump

```shell
# 安装完成后正式安装 Elasticsearch-dump
# wins 安装 nodejs 并配置可以跳转到: https://www.jianshu.com/p/7d60bf86bc0b
npm install elasticdump -g
elasticdump
```

> 创建几条基础数据

```json
POST /index_stu/stu {"name": "name01", "age": 1, "gender": "男", "remark": "remark01"}
POST /index_stu/stu {"name": "name02", "age": 2, "gender": "男", "remark": "remark02"}
POST /index_stu/stu {"name": "name03", "age": 3, "gender": "男", "remark": "remark03"}
```

> 将 es 中的数据写入文件

```shell
# 备份的三个流程 settings > mapping > data
elasticdump 
    --input=http://<ip>:<port>/<index> # 从哪里读数据 
    --output=/es/data/<index>_settings.json # 将数据到哪里 
    --type=settings # 写出什么类型的数据

elasticdump 
    --input=http://<ip>:<port>/<index> # 从哪里读数据 
    --output=/es/data/<index>_settings.json # 将数据到哪里 
    --type=mapping  # 写出什么类型的数据

elasticdump 
    --input=http://<ip>:<port>/<index> # 从哪里读数据 
    --output=/es/data/<index>_<type>.json # 将数据到哪里 
    --type=data # 写出什么类型的数据
    --limit=10000 # 每次处理的数据量

# 恢复备份的步骤

elasticdump
    --input=/es/data/<index>_<type>.json
    --output=http://<ip>:<port>/<index>
    --type=settings

elasticdump
    --input=/es/data/<index>_<type>.json
    --output=http://<ip>:<port>/<index>
    --type=mapping

elasticdump
    --input=/es/data/<index>_<type>.json
    --output=http://<ip>:<port>/<index>
    --type=data
    --limit=10000

# --------
	
elasticdump
	--input=http://<ip>:<port>/<index>
	--output=/es/data/<index>_settings.json
	--searchBody='{"query":{"term":{"name": "name02"}}}' # 筛选规则

elasticdump
	--input=http://<ip>:<port>/<index>
	--output=/es/data/<index>_settings.json
	--params='{"preference" : "_shards:0"}'

elasticdump
	--input=http://<ip>:<port>/<index>
	--output=/es/data/<index>_settings.json
	--fileSize=10mb # 设置文件大小

elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=query.json \
  --searchBody=@/data/searchbody.json  

elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=$ \
  | gzip > /data/my_index.json.gz
```

* type 类型

| 类型     | 描述               |
| -------- | ------------------ |
| settings | 备份索引的settings |
| mapping  | 备份数据的mapping  |
| data     | 最后备份数据       |
| analyzer |                    |
| template |                    |
| alias    |                    |

> 迁移最佳顺序为：settings > mapping > data
>
> **注意**: 直接迁移 mapping 或者 data 将失去原有集群中索引的配置信息会导致分片数量和副本数量等问题

* 认证授权 : https://github.com/elasticsearch-dump/elasticsearch-dump/wiki

> url 认证

```shell
elasticdump \
  --input=http://username:password@localhost:9200 \
  --input-index=demo/dump \
  --output=http://user:pass@192.168.0.2:9200 \
  --output-index=demo/dump \
  --type=data
```

> 使用授权文件

```shell
elasticdump \
  --input=http://localhost:9200 \
  --input-index=demo/dump \
  --httpAuthFile=/path/to/your/http_auth_file \
  --output=http://192.168.0.2:9200 \
  --output-index=demo/dump \
  --type=data
```

* http_auth_file中的内容。
* 注意：以文件方式，你的所有服务器必须使用相同的基本认证。

```json
user=<username>
password=<password>
```