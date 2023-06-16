

> ## ElasticSearch 配置 X-Pack

> [配置ElasticSearch用户密码](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/setup-xpack.html)

* 在 es `elasticsearch.yml` 配置文件中添加配置启动 xpack

```yaml
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
```

* 配置用户密码

```shell
bin/elasticsearch-setup-passwords interactive
```

* `auto` - Uses randomly generated passwords（自动生成密码）
* `interactive` - Uses passwords entered by a user（交互式给每个用户设置密码）

> XPack 用户 [Api](https://www.elastic.co/guide/en/elasticsearch/reference/6.2/security-api-users.html)

```json
# 创建用户
POST /_xpack/security/user/jacknich
{
  "password" : "j@rV1s",
  "roles" : [ "admin", "other_role1" ],
  "full_name" : "Jack Nicholson",
  "email" : "jacknich@example.com",
  "metadata" : {
    "intelligence" : 7
  }
}

# 查看所有用户
GET /_xpack/security/user

# 修改用户密码 模板: /_xpack/security/user/<username>/_password
PUT /_xpack/security/user/jacknich/_password
{
  "password" : "s3cr3t"
}

# 删除用户
DELETE /_xpack/security/user/jacknich

## 系统默认用户与roles权限
"username" : "elastic", "roles" : [ "superuser" ]
"username" : "kibana_system", "roles" : [ "kibana_system" ]#kibana在连接时需要使用
"username" : "logstash_system", "roles" : [ "logstash_system" ]#logstash在连接时需要使用
 . . .
```

> [在传输层上启用TLS - 必需](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/configuring-tls.html)

* 在 es `elasticsearch.yml` 配置文件中添加配置启动 xpack

```yaml
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
```

* `ca`-生成新的本地证书颁发机构

```shell
$ bin/elasticsearch-certutil ca
```

* `cert`-生成X.509证书和密钥

```shell
$ bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12
```

* 此时会生成两个文件
  * elastic-certificates.p12
  * elastic-stack-ca.p12
* 将 `elastic-certificates.p12` 文件拷贝到每台机器上的 config 目录并添加 elasticsearch.yml 配置

```yaml
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
```

* 在每个节点中设置证书密码

```shell
# 对应的证书密码: xpack.security.transport.ssl.keystore.path
bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password

# 对应的证书密码: xpack.security.transport.ssl.truststore.path
bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password
```

* 重启 ElasticSearch,  基于tcp协议的加密就配置好了

> [在HTTP层上启用TLS - 建议](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/configuring-tls.html#tls-http)

* 继续使用 PKCS#12 格式的证书

```shell
$ bin/elasticsearch-certutil ca
$ bin/elasticsearch-certutil cert --ca elastic-stack-ca.p12
## 修改文件名为 http-ca.p12 & http.p12
```

* 将文件拷贝到各集群 config 目录下
* 在 elasticsearch.yaml 中添加配置

```yaml
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.keystore.path: "http.p12"
```

* 在每个节点中设置证书密码

```shell
bin/elasticsearch-keystore add xpack.security.http.ssl.keystore.secure_password
```

* 重启ElasticSearch，这时需要通过 https 协议访问 https://localhost:9200
* http 层面的加密更推荐使用官方文档中提供的 PEM 格式的密钥有兴趣的小伙伴可以自行研究

> bin/elasticsearch-certutil 指令的参数

1. `csr`-生成证书签名请求
2. `cert`-生成X.509证书和密钥
3. `ca`-生成新的本地证书颁发机构
4. `http`-为Elasticsearch http接口生成新证书（或证书请求）

> SpringBoot 连接到 ElasticSearch

* 基于 http [elasticsearch-rest-high-level-client](https://gitee.com/jiangruyi/notebook/blob/master/ElasticSearch/08-elasticsearch-rest-client-api.md)

---


* 基于 tcp , 添加远程仓库

```xml
<repositories>
    <repository>
        <id>elasticsearch-releases</id>
        <url>https://artifacts.elastic.co/maven</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

* 导入 spring-data-elasticsearch 依赖

```xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-elasticsearch</artifactId>
    <version>4.0.3.RELEASE</version>
    <exclusions>
        <exclusion>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>transport</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>x-pack-transport</artifactId>
    <version>7.9.1</version>
</dependency>
```

* 创建实体类 

```java
package com.example.esclient.domain;

@Data
@Document(indexName = "demo_index")
public class DemoDomain {
    @Field
    private String id;
    private String name;
}
```

* 创建 Repository

```java
package com.example.esclient.repository;

public interface DemoDomainRepository extends ElasticsearchRepository<DemoDomain, String> {
}
```

* 创建 ElasticSearch 配置类

```java
package com.example.esclient;

import org.elasticsearch.client.transport.TransportClient;
import org.elasticsearch.common.settings.Settings;
import org.elasticsearch.common.transport.TransportAddress;
import org.elasticsearch.xpack.client.PreBuiltXPackTransportClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.elasticsearch.repository.config.EnableElasticsearchRepositories;

import java.net.InetAddress;
import java.net.UnknownHostException;

@Configuration
@EnableElasticsearchRepositories(basePackages = "com.example.esclient.repository")
public class ElasticsearchConfig {

    @Value("${spring.data.elasticsearch.cluster-nodes}")
    private String hostPorts;

    @Value("${spring.data.elasticsearch.cluster-name}")
    private String clusterName;

    /**
     * 账号:密码
     **/
    private final String XPACK_SECURITY_USER = "<username>:<password>";

    /**
     * 构建连接地址
     **/
    private TransportAddress[] hostList() throws UnknownHostException {
        TransportAddress[] addressList = new TransportAddress[3];
        String[] split = hostPorts.split(",");
        for (int i = 0; i < split.length; i++) {
            String s = split[i];
            String[] split1 = s.split(":");
            addressList[i] = new TransportAddress(InetAddress.getByName(split1[0]), Integer.valueOf(split1[1]));
        }
        return addressList;
    }


    @Bean
    public TransportClient transportClient() throws Exception {
        TransportClient client = new PreBuiltXPackTransportClient(Settings.builder()
                .put("cluster.name", clusterName)
                .put("xpack.security.user", XPACK_SECURITY_USER)
                .put("client.transport.sniff", false)
                .put("xpack.security.enabled", true)
                .put("xpack.security.transport.ssl.enabled", true)
                .put("xpack.security.transport.ssl.keystore.path", "/ELK/cluster/config/elastic-certificates.p12")
                .put("xpack.security.transport.ssl.keystore.password", "<certificates.p12证书的密码>")
                .put("xpack.security.transport.ssl.verification_mode", "certificate")
                .build())
                .addTransportAddresses(hostList());
        return client;
    }

}


```

* 配置文件中配置

```properties
spring.data.elasticsearch.cluster-name=my-application
spring.data.elasticsearch.cluster-nodes=127.0.0.1:9300,127.0.0.1:9301,127.0.0.1:9302
spring.data.elasticsearch.repositories.enabled=true
```

* 测试

```java
@Autowired
private DemoDomainRepository demoDomainRepository;

@Test
public void saveAndSearch() {
    QueryBuilder queryBuilder = QueryBuilders.boolQuery();
    Iterable<DemoDomain> search = demoDomainRepository.search(queryBuilder);
    for (DemoDomain domain : search) {
        System.out.println(domain);
    }
}
```
