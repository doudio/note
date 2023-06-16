> java导出word Poi-tl方式

* 官方项目地址: https://github.com/Sayi/poi-tl
* 中文文档地址: http://deepoove.com/poi-tl
* 开源中国介绍: https://www.oschina.net/p/poi-tl

> 使用遇到的第一个问题

```java
java.lang.NoSuchMethodError: org.apache.logging.log4j.Logger.atDebug()Lorg/apache/logging/log4j/LogBuilder;

	at org.apache.poi.openxml4j.opc.PackageRelationshipCollection.parseRelationshipsPart(PackageRelationshipCollection.java:309)
```

> 项目中的log4j版本问题

```xml
<version>2.3.12.RELEASE</version>

<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.8.4</version>
</dependency>

<dependency>
    <groupId>com.deepoove</groupId>
    <artifactId>poi-tl</artifactId>
    <version>1.12.1</version>
</dependency>
```

> 官方文档写的很详细了, 就不浪费时间了

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
static class BookItm {

    private String name;
    private BigDecimal price;
    private Integer sell;
    private LocalDate publish;

}

@Test
void all() throws Exception {
    InputStream templateStream = ResourceUtil.getStream("template/all.docx");

    Map<String, Object> dataMap = new HashMap<>();

    dataMap.put("logo", Pictures.ofUrl("https://img10.360buyimg.com/img/jfs/t1/192028/25/33459/5661/63fc2af2F1f6ae1b6/d0e4fdc2f126cbf5.png").size(120, 120).create());
    dataMap.put("title", "北京地区");
    dataMap.put("book", ListUtil.of(
            new BookItm("《Java 核心技术卷 1+卷 2》", new BigDecimal("149"), 9999, LocalDate.of(2019, 11, 25)),
            new BookItm("《Java 并发实现原理：JDK 源码剖析》", new BigDecimal("89.9"), 8888, LocalDate.of(2020, 4, 1)),
            new BookItm("《深入理解 Java 虚拟机》", new BigDecimal("129.00"), 7777, LocalDate.of(2020, 4, 1)),
            new BookItm("《MySQL 是怎样运行的》", new BigDecimal("99.99"), 6666, LocalDate.of(2020, 11, 1))
    ));
    dataMap.put("systemOS", DateUtil.date());

    LoopRowTableRenderPolicy policy = new LoopRowTableRenderPolicy();

    ConfigureBuilder builder = Configure.builder();

    // 在模板标签中使用SpringEL表达式
    builder.useSpringEL();
    builder.bind("book", policy);

    XWPFTemplate template = XWPFTemplate.compile(templateStream, builder.build()).render(dataMap);
    template.writeAndClose(new FileOutputStream("G:\\temp\\word\\output" + System.currentTimeMillis() + ".docx"));
}
```