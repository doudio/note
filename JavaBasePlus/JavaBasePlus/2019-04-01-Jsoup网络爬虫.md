> #### Jsoup 网络爬虫

> 添加 `maven` 配置

```xml
<!-- 解析数据 -->
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.8.3</version>
</dependency>

<!-- 爬网页 -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.6</version>
</dependency>
```

> 使用 `HttpClient` 发起请求获取页面数据

```
HttpGet httpGet = new HttpGet("https://www.baidu.com/");

HttpHost proxy = new HttpHost("125.70.13.77", 8080);// 使用代理服务器发送请求
httpGet.setConfig(RequestConfig.custom()
                  .setSocketTimeout(30000)// 发送时间
                  .setConnectTimeout(30000)// 连接时间
                  .setProxy(proxy)// 设置代理服务器
                  .build());

CloseableHttpClient httpClient = HttpClientBuilder.create().build();
HttpClientContext context = HttpClientContext.create();

CloseableHttpResponse response = httpClient.execute(httpGet, context);

HttpEntity entity = response.getEntity();
String string = EntityUtils.toString(entity, "utf-8");

System.out.println(string);
```

1. 使用代理防止被一些反扒网站拉黑: [代理服务器](http://www.xicidaili.com/)

> 使用`Jsoup`发起请求并且解析页面数据

```java
// 获取 Document 对象
Document document = Jsoup.connect("https://www.baidu.com/").get();

// 通过 CSS 选择器, 匹配标签数据
Elements select = document.select("a");

for (Element element : select) {
    System.out.println(element);
}
```

1. `Document`对象中有各种`JavaScript 和 CSS`解析方法

> 一般发送请求使用`httpclient`解析网页数据使用`jsoup`, 两者组合使用

```java
HttpGet httpGet = new HttpGet("https://www.baidu.com/");

HttpHost proxy = new HttpHost("125.70.13.77", 8080);
httpGet.setConfig(RequestConfig.custom()
                  .setSocketTimeout(30000)
                  .setConnectTimeout(30000)
                  .setProxy(proxy)
                  .build());

CloseableHttpClient httpClient = HttpClientBuilder.create().build();
HttpClientContext context = HttpClientContext.create();

CloseableHttpResponse response = httpClient.execute(httpGet, context);

HttpEntity entity = response.getEntity();
String htmlData = EntityUtils.toString(entity, "utf-8");

Document document = Jsoup.parse(htmlData);

Elements select = document.select("a");

for (Element element : select) {
    System.out.println(element);
}
```

