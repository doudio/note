> ## elasticsearch-rest-high-level-client api

[TOC]



* 官方给的5.5.3的API并不支持高级API, 发现只有5.6以上的版本支持
* [官方文档位置](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/5.6/java-rest-high.html)

> 导入依赖

```xml
<properties>
    <!-- 建议和es版本保持一致 -->
    <elasticsearch.version>6.2.2</elasticsearch.version>
</properties>

<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>${elasticsearch.version}</version>
</dependency>
```

> 在配置文件中添加es配置

```properties
# 设置超时时间默认1s
#spring.elasticsearch.rest.connection-timeout
# 设置连接用户
#spring.elasticsearch.rest.username
# 设置连接密码
#spring.elasticsearch.rest.password
# 设置es读取数据的超时时间默认30s
#spring.elasticsearch.rest.read-timeout
# 设置es地址多个以,分隔
spring.elasticsearch.rest.uris=127.0.0.1:9201,127.0.0.1:9202,127.0.0.1:9203
```

### 编写代码测试

#### count 统计

```java
// 初始化查询器 并制定 es 索引
SearchRequest request = new SearchRequest()
    .indices(EsConsUtils.INDEX).types(EsConsUtils.INDEX_DOC);

// 初始化条件构造器
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
// 指定返回 0 条记录
sourceBuilder.size(0);
// 定义普通查询
BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
boolQuery.must(QueryBuilders.termQuery("prId", 1688));

// 定义聚合查询
ValueCountAggregationBuilder terms_city = AggregationBuilders.count("count_city").field("id");

// 添加查询条件
sourceBuilder.query(boolQuery);
sourceBuilder.aggregation(terms_city);
request.source(sourceBuilder);

// 发起请求并获取返回值
try {
    SearchResponse response = restHighLevelClient.search(request);
    ValueCount count_city = response.getAggregations().get("count_city");
    return count_city.getValue();
} catch (IOException e) {
    e.printStackTrace();
}
```

#### 对查询进行分组

```java
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();

sourceBuilder.size(0);

BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();

boolQuery.must(QueryBuilders.termQuery("proId", "520000"));
sourceBuilder.query(boolQuery);

TermsAggregationBuilder termsCity = AggregationBuilders.terms("terms_city").field("city.keyword").size(100);

sourceBuilder.aggregation(termsCity);

SearchRequest request = new SearchRequest().indices(EsConsUtils.INDEX).types(EsConsUtils.INDEX_DOC);
request.source(sourceBuilder);

SearchResponse response = restHighLevelClient.search(request);
ParsedStringTerms terms = response.getAggregations().get("terms_city");

JSONObject result = new JSONObject();
for (Terms.Bucket bucket : terms.getBuckets()) {
    result.put(bucket.getKeyAsString(),bucket.getDocCount());
}
```

#### 普通查询

```java
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));

BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
boolQuery.must(QueryBuilders.termQuery("provinceId", 410000));

sourceBuilder.query(boolQuery);
request.source(sourceBuilder);
List<SysLogES> esList = new ArrayList();
SearchResponse response = restHighLevelClient.search(request);

Arrays.stream(response.getHits().getHits())
    .forEach(i -> {
        String sourceAsString = i.getSourceAsString();
        JSONObject resJson = (JSONObject) JSONObject.parse(sourceAsString);
        SysLogES logES = resJson.toJavaObject(SysLogES.class);
        esList.add(logES);
    });

System.out.println(JSON.toJSONString(esList));
```

#### 新增数据 如果id一致就会触发修改操作

```java
IndexRequest request = new IndexRequest(EsConsUtils.INDEX, EsConsUtils.INDEX_DOC, "10010");

String jsonStr = "{id:10010,name:zs}";
request.source(jsonStr, XContentType.JSON);

IndexResponse indexResponse = restHighLevelClient.index(request, RequestOptions.DEFAULT);

String resIndex = indexResponse.getIndex();
String resType = indexResponse.getType();
String resId = indexResponse.getId();
long resVersion = indexResponse.getVersion();
if (indexResponse.getResult() == DocWriteResponse.Result.CREATED) {
    System.out.println("新增文档成功!" + resIndex + resType + resId + resVersion);
} else if (indexResponse.getResult() == DocWriteResponse.Result.UPDATED) {
    System.out.println("修改文档成功!");
}
```

#### 批量新增

```java
BulkRequest request = new BulkRequest();

List<RestClientTest> list = new ArrayList<>();
for (int i = 0; i < 10; i++) {
    list.add(new RestClientTest(i, "hashVal00" + i, "zs" + i));
}

for (RestClientTest rest : list) {
    request.add(new IndexRequest(EsConsUtils.INDEX, 
                                 EsConsUtils.INDEX_DOC, 
                                 String.valueOf(rest.getId()))
                .source(JSON.toJSONString(rest), XContentType.JSON));
}

BulkResponse bulkResponse = restHighLevelClient.bulk(request, RequestOptions.DEFAULT);
for (BulkItemResponse bulkItemResponse : bulkResponse) {
    DocWriteResponse itemResponse = bulkItemResponse.getResponse();
    if (bulkItemResponse.getOpType() == DocWriteRequest.OpType.INDEX
        || bulkItemResponse.getOpType() == DocWriteRequest.OpType.CREATE) {
        IndexResponse indexResponse = (IndexResponse) itemResponse;
        //TODO 新增成功的处理
        System.out.println("新增成功,{}" + indexResponse.toString());
    } else if (bulkItemResponse.getOpType() == DocWriteRequest.OpType.UPDATE) {
        UpdateResponse updateResponse = (UpdateResponse) itemResponse;
        //TODO 修改成功的处理
        System.out.println("修改成功,{}" + updateResponse.toString());
    } else if (bulkItemResponse.getOpType() == DocWriteRequest.OpType.DELETE) {
        DeleteResponse deleteResponse = (DeleteResponse) itemResponse;
        //TODO 删除成功的处理
        System.out.println("删除成功,{}" + deleteResponse.toString());
    }
}
```

#### 根据id删除数据

```jaa
DeleteRequest deleteRequest = new DeleteRequest(EsConsUtils.INDEX, EsConsUtils.INDEX_DOC,  "10086");
DeleteResponse response = null;
try {
response = restHighLevelClient.delete(deleteRequest, RequestOptions.DEFAULT);
} catch (IOException e) {
e.printStackTrace();
}
System.out.println(response);
```

#### 根据其他字段来批量删除数据

```java
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name.keyword", "zhangshan");
sourceBuilder.query(termQueryBuilder);
SearchRequest searchRequest = new SearchRequest(EsConsUtils.INDEX);
searchRequest.types(EsConsUtils.INDEX_DOC);
searchRequest.source(sourceBuilder);
SearchResponse response = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);

SearchHits hits = response.getHits();
List<String> docIds = new ArrayList<>(hits.getHits().length);
for (SearchHit hit : hits) {
    docIds.add(hit.getId());
}

BulkRequest bulkRequest = new BulkRequest();
for (String id : docIds) {
    DeleteRequest deleteRequest = new DeleteRequest(EsConsUtils.INDEX, EsConsUtils.INDEX_DOC, id);
    bulkRequest.add(deleteRequest);
}
restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT);
```

> 根据日期分组统计

```java
   /**
     * DSL 语句
     *
     * 这个可以不设置桶大小
     *
     * GET /my_index/_search
     * {
     *   "size": 0,
     *   "aggs": {
     *     "time": {
     *       "date_histogram": {
     *         "field": "inserEsTime",
     *         "interval": "day",
     *         "format": "yyyy-MM-dd"
     *       }
     *     }
     *   }
     * }
     */
    @Test
    void dateHistogram() {
        // 初始化查询器 并制定 es 索引
        SearchRequest request = new SearchRequest()
                .indices(EsIndexEnum.my_index.getIndex())
                .types(EsIndexEnum.my_index.getType());

        // 初始化条件构造器
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.size(0);

        BoolQueryBuilder builder = QueryBuilders.boolQuery();

        DateHistogramAggregationBuilder dateAgg = AggregationBuilders
                .dateHistogram("time")/*定义聚合结果名*/
                .field("inserEsTime")/*定义字段*/
                .dateHistogramInterval(DateHistogramInterval.DAY)/*时间单位, 点进去还有很多*/
                .format(YMD_FORMAT)/*时间格式*/
                .timeZone(DateTimeZone.forTimeZone(TimeZone.getTimeZone(TIME_ZONE)));/*添加时区*/

        // 添加查询条件与聚合规则
        sourceBuilder.query(builder);
        sourceBuilder.aggregation(dateAgg);
        request.source(sourceBuilder);

        try {
            SearchResponse response = restHighLevelClient.search(request, RequestOptions.DEFAULT);
            ParsedDateHistogram time = response.getAggregations().get("time");
            Map<String, Long> bucketMap = new HashMap<>();
            for (Histogram.Bucket bucket : time.getBuckets()) {
                bucketMap.put(bucket.getKeyAsString(), bucket.getDocCount());
            }
            log.info("result.bucketMap: {}", bucketMap);
        } catch (IOException e) {
            log.error(e.getMessage(), e);
        }
    }
```

> ES 游标查询

```java
    /** 
     * DSL 语句
     * 1. 创建第一次的游标查询
     * 2. 1m表示:过期时间1分钟
     *
     * GET my_index/_search?scroll=1m
     * {
     *   "size": 100
     * }
     *
     * 3. 得到第一次查询的游标ID
     * 4. 随后一直通过这样的方式走向下一批数据
     *
     * GET _search/scroll
     * {
     *   "scroll":"1m",
     *   "scroll_id":"DnF1ZXJ5VGhlbkZldGNoCQAAAAAAfYetFnJzM2FmbnoxUU51a2d1QXB6ZlBORVEAAAAAAIHNkxZrZ0ZCdHdyc1JrLXFWTUJ1TlI5MjdRAAAAAACBzZEWa2dGQnR3cnNSay1xVk1CdU5SOTI3UQAAAAAAgc2QFmtnRkJ0d3JzUmstcVZNQnVOUjkyN1EAAAAAAIJJABZabHNoaE9zeFRiV0RIQ1RkRXpvQmdBAAAAAAB9h6wWcnMzYWZuejFRTnVrZ3VBcHpmUE5FUQAAAAAAgc2UFmtnRkJ0d3JzUmstcVZNQnVOUjkyN1EAAAAAAIHNkhZrZ0ZCdHdyc1JrLXFWTUJ1TlI5MjdRAAAAAACBzZUWa2dGQnR3cnNSay1xVk1CdU5SOTI3UQ=="
     * }
     */
    @Test
    void scrollId() {
        SearchRequest request = new SearchRequest()
                .indices(EsIndexEnum.my_index.getIndex())
                .types(EsIndexEnum.my_index.getType());

        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.size(1000);
        request.source(sourceBuilder);
        request.scroll(TimeValue.timeValueMinutes(2));
        SearchResponse response = null;

        try {
            response = restHighLevelClient.search(request, RequestOptions.DEFAULT);

            String scrollId = response.getScrollId();// 获取到第一次的游标ID
            SearchHit[] result = response.getHits().getHits();// 第一次查询的结果

            SearchScrollRequest scrollRequestSub = new SearchScrollRequest();
            //设置游标有效期
            scrollRequestSub.scrollId(scrollId);
            scrollRequestSub.scroll(TimeValue.timeValueMinutes(2));
            for (SearchHit[] resultSub = result /*将第一次查询结果作为初始值*/; isNotEmpty(resultSub) /*如果返回结果不为空就继续查询下去*/;
                 resultSub = restHighLevelClient.scroll(scrollRequestSub, RequestOptions.DEFAULT).getHits().getHits() /*迭代下一批结果*/) {
                for (SearchHit hit : resultSub) {
                    String sourceAsString = hit.getSourceAsString();
                    System.out.println(sourceAsString);
                }
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    private boolean isNotEmpty(SearchHit[] resultSub) {
        if (resultSub != null) {
            if (resultSub.length > 0) return true;
        }
        return false;
    }
```

#### Request 对象

| 对象          | 操作 |
| ------------- | ---- |
| IndexRequest  | 新增 |
| DeleteRequest | 删除 |
| SearchRequest | 查询 |
| UpdateRequest | 修改 |

### 将毫秒值在查询出来时转换为 Date

```java
@Data
public class ChineseDateFormat extends SimpleDateFormat {

    private static final long serialVersionUID = 6276491293753307748L;

    public ChineseDateFormat() throws Exception{
        super("y-MM-dd HH:mm:ss", Locale.CHINESE);
        setTimeZone(TimeZone.getTimeZone("GMT+8"));
    }
    
}
```

#### yaml 中配置依赖

```yaml
spring:
  jackson:
    date-format: com.config.ChineseDateFormat
```

### 在 springboot + reids + elasticsearch 整合是会出现 es 与 redis 中的 netty 包冲突

#### 在springboot启动程序前添加

```java
System.setProperty("es.set.netty.runtime.available.processors", "false");
```

#### 在配置类中添加配置, 加两个的原因是因为如果运行测试方法项目加载顺序的问题

```java
@Configuration
public class ElasticsearchConfig {

    @PostConstruct
    void init() {
        System.setProperty("es.set.netty.runtime.available.processors", "false");
    }

}
```