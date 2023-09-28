> ## ElasticSearch Java IN Search

> 导入`pom`文件

```xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-elasticsearch</artifactId>
</dependency>
```

> 配置`application.yml`

```yaml
spring:
  data:
    elasticsearch:
      cluster-name: clusterName
      cluster-nodes: <IP>:<PORT>
      repositories:
        enabled: true
      properties:
        node:
          master: false
          name: master
```

> 编写索引类

```java
@Document(indexName = "myIndex", type = "index_info")
public class MyIndex {
    @Id
    private Integer id;
    private String name;
}
```

> 编写`Repository`

```java
@Repository
public interface MyRepository extends ElasticsearchRepository<MyIndex, Integer> {
}
```

> 编写`Java Search`

> 查询某个字段匹配多个值： `SELECT id, name FROM MyIndex WHERE field IN ('V1', 'V2')`

```java
BoolQueryBuilder queryBuilder = QueryBuilders.boolQuery();
queryBuilder.should(QueryBuilders.matchQuery("field", Arrays.asList("V1","V2")).boost(1));
Iterable<Object> search = myRepository.search(customQq);
search.forEach(System.out::println);
```

---

> #### 通过 TransportClient 去 ES 查询

```java
@Autowired
private TransportClient transportClient;

public double countReply() {
    BoolQueryBuilder builder = QueryBuilders.boolQuery();

    ValueCountAggregationBuilder count_number = AggregationBuilders.count("count_number").field("count_number");

    SearchResponse response = transportClient.prepareSearch("index")
        .setTypes("doc")
        .setQuery(builder)
        .setSize(0)
        .addAggregation(count_number)
        .get();

    ValueCount countMoney = response.getAggregations().get("count_number");
    return countMoney.getValue();
}
```

> ElasticsearchRepository 的 api

```java
Pageable pageable = PageRequest.of(dto.getPage(), dto.getLimit());
BoolQueryBuilder esBuilder = QueryBuilders.boolQuery();

replyTelephoneEsBuilder.must(QueryBuilders.matchPhraseQuery("type", 0);

if (StringUtils.isNotBlank(dto.getStartDate()) &&
    StringUtils.isNotBlank(dto.getEndDate())) {
    long startTime = TimeUtil.timeStr2Mills(dto.getStartDate(), TimeUtil.DateFormatter.FORMAT_YMD);
    long endTime = TimeUtil.plusTimeAndConvertMills(dto.getEndDate(), TimeUtil.DateFormatter.FORMAT_YMD, 1);
    esBuilder.must(QueryBuilders.rangeQuery("insertTime")
                                 .gte(startTime)
                                 .lt(endTime));
}

if (StringUtils.isNotBlank(dto.getVisitId())) {
    esBuilder.must(QueryBuilders.wildcardQuery("name.keyword", String.format("*%s*", dto.getName())));
}
                             
if (StringUtils.isNotBlank(dto.getVictimName())) {
    BoolQueryBuilder builder = QueryBuilders.boolQuery();
    String trim = dto.getVictimName().trim();
    builder.should(
        QueryBuilders.wildcardQuery("cardid.keyword", String.format("*%s*", trim))
    ).should(
        QueryBuilders.wildcardQuery("cardAddr.keyword", String.format("*%s*", trim))
    );
    esBuilder.must(builder);
}

if (StringUtils.isNotBlank(dto.getCity())) {
    replyTelephoneEsBuilder.must(QueryBuilders.matchPhraseQuery("cityId.keyword", dto.getCity()));
}

String statues = dto.statues();
if (StringUtils.isNotBlank(replyState)) {
    replyTelephoneEsBuilder.must(QueryBuilders.termsQuery("statues", replyState.split(",")));
}
                             
FieldSortBuilder timeSortDesc = SortBuilders.fieldSort("time").order(SortOrder.DESC);

SearchQuery searchQuery = new NativeSearchQueryBuilder()
    .withQuery(esBuilder)
    .withSort(timeSortDesc)
    .withPageable(pageable)
    .build();

Page<BeanEs> search = esRepository.search(searchQuery);
```

* Pageable 分页默认不会减1, 需要手动减1