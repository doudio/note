> ### 通过 HttpClient 来模拟登录与设置代理

* 通过HttpClient模拟浏览器请求
* 通过jsoup来解析页面
* 通过代理隐藏自己IP
* 去大型免费代理平台去拉取代理

> 需要导入的依赖

```xml
<!-- HttpClient :: 发起请求 -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.10</version>
</dependency>

<!-- Httpmime :: 请求时间 -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpmime</artifactId>
    <version>4.5.10</version>
</dependency>

<!-- jsoup :: 解析html内容 -->
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.12.1</version>
</dependency>

<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.3.2</version>
</dependency>
```

> 使用HttpClient模拟请求并设置代理访问

```java
public class HttpProxyClientUtils {

    // 通过访问这个网站获取自己请求的IP地址
    public static final String TEST_PROXY_URL = "http://pv.sohu.com/cityjson?ie=utf-8";

    // 设置（请求，连接，传输）超时
    public static final Integer SOCKET_TIMEOUT = 1000 * 5;
    public static final Integer CONNECT_TIMEOUT = SOCKET_TIMEOUT;
    public static final Integer CONNECTION_REQUEST_TIMEOUT = SOCKET_TIMEOUT;

    // HttpClient 客户端
    private static CloseableHttpClient httpClient = HttpClients.createDefault();
    // 代理服务器
    private HttpHost proxy;
    // 保存的 Cookie
    private String cookies = "";

    /**
     * 模拟表单提交来进行登录
     */
    public String verifyLogin(String url, String username, String password) {
        log.debug("验证登录({}), username: {}, password: {}", url, username, password);

        String result = "";
        HttpPost httpPost = new HttpPost(url);
        // 请求时携带 Cookie
        httpPost.setHeader("Cookie", "PHPSESSID=" + getPHPSSID() + ";e=0|0");
        List<NameValuePair> nvps = Arrays.asList(
                new BasicNameValuePair("username", username),
                new BasicNameValuePair("password", password)
        );

        HttpEntity reqEntity = new UrlEncodedFormEntity(nvps, Consts.UTF_8);
        httpPost.setEntity(reqEntity);
       
        // 设置代理与请求超时时间
        httpPost.setConfig(RequestConfig.custom().setProxy(proxy).setSocketTimeout(SOCKET_TIMEOUT).setConnectTimeout(CONNECT_TIMEOUT).setConnectionRequestTimeout(CONNECTION_REQUEST_TIMEOUT).setStaleConnectionCheckEnabled(true).build());

        try (CloseableHttpResponse response = httpClient.execute(httpPost)) {
            HttpEntity respEntity = response.getEntity();
            result = EntityUtils.toString(respEntity, Charset.forName("utf-8"));
            EntityUtils.consume(respEntity);
        } catch (org.apache.http.NoHttpResponseException n) {
            log.error("代理不能使用了::{}", n);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return result;
    }

    public String getData(String url) {
        log.debug("GetData::{}", url);
        HttpGet httpGet = new HttpGet(url);
        httpGet.setConfig(RequestConfig.custom().setProxy(proxy).setSocketTimeout(SOCKET_TIMEOUT).setConnectTimeout(CONNECT_TIMEOUT).setConnectionRequestTimeout(CONNECTION_REQUEST_TIMEOUT).setStaleConnectionCheckEnabled(true).build());
        
        httpGet.setHeader("Cookie", "cookie1=val;cookie2=val2");

        try (CloseableHttpResponse response = httpClient.execute(httpGet)) {
            int statusCode = response.getStatusLine().getStatusCode();
            if (statusCode > 299 || statusCode < 200) {
                throw new IOException("statusCode error : " + statusCode);
            }
            return entityToString(response.getEntity());
        } catch (org.apache.http.NoHttpResponseException n) {
            log.error("代理不能使用了::{}", n);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return "";
    }

    public static String entityToString(HttpEntity entity) throws IOException {
        StringBuilder stringBuilder = new StringBuilder();
        BufferedReader br = new BufferedReader(new InputStreamReader(entity.getContent()));
        String line = null;
        while ((line = br.readLine()) != null) {
            stringBuilder.append(line);
        }
        EntityUtils.consume(entity);
        return stringBuilder.toString();
    }
    
}
```

> 去免费代理网站中获取代理

| 网站                                                         | 正则表达式                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `http://www.ip3366.net/free/?stype=1&page=2`                 | `<td>(\d+\.\d+\.\d+\.\d+)</td>\s+<td>(\d+)</td>`             |
| `http://www.iphai.com/free/ng`                               | `<td>\s+(\d+\.\d+\.\d+\.\d+)\s+</td>\s+<td>\s+(\d+)\s+</td>` |
| `http://www.66ip.cn/nmtq.php?getnum=100&isp=0&anonymoustype=3&start=&ports=&export=&ipaddress=&area=1&proxytype=2&api=66ip` | `(\d+\.\d+\.\d+\.\d+):(\d+)<br/>`                            |
| `http://www.xicidaili.com/nn/1`                              | `<td>(\d+\.\d+\.\d+\.\d+)</td>\s+<td>(\d+)</td>`             |

