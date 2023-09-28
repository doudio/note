> ## SpringCloudAlibaba-服务熔断限流路由转发鉴权

> ##### 服务熔断：在服务消费者中加入依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
    <version>${spring-cloud-alibaba.version}</version>
</dependency>
```

> 在 bootstrap.yml 文件中添加配置

```yaml
feign:
  sentinel:
    enabled: true

# 
ribbon:
  ConnectTimeout: 2000
  ReadTimeout: 5000
```

> 在  @FeignClient 注解中添加接口实现类

```java
@FeignClient(name = "nacos-server-provider", fallback = NacosServerProviderFallback.class)
public interface NacosServerProvider {

    @GetMapping("/echo/{str}") String echo(@PathVariable("str") String str);

}
```

> 编写实现类

```java
@Component
public class NacosServerProviderFallback implements NacosServerProvider {
    @Override
    public String echo(String str) {
        return "echo fallback";
    }
}
```

> ##### 服务限流: 在服务提供者中加入依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
    <version>2.1.0.RELEASE</version>
</dependency>

<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
    <version>1.7.0</version>
</dependency>
```

> 在 bootstrap.yml 文件中添加配置

```yaml
spring:
  application:
    name: nacos-server-provider
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
      discovery:
        server-addr: 127.0.0.1:8848
    sentinel:
      datasource:
        ds1:
          nacos:
            server-addr: 127.0.0.1:8848
            dataId: ${spring.application.name}-flow-rules
            data-type: json
            rule-type: flow
```

> 在需要限流的方法中添加注解

```java
@Data
@RestController
@RefreshScope
public class EchoController {
    @Value("${server.port}")
    private String port;
    @Value("${time:1}")
    private int time;

    @GetMapping(value = "/echo/{string}")
    @SentinelResource(value = "protected-resource", blockHandler = "handleBlock")
    public String echo(@PathVariable String string) throws InterruptedException {
        String result = string + "(" + port + "):[time:" + time + "]";
        System.out.println(result);
        Thread.sleep(time);
        return result;
    }

    public String handleBlock(String string, BlockException be) {
        return "BlockException::"+be.getMessage();
    }
}
```

> 在去 Nacos 中动态配置限流规则

![](img/nacos-ui-config.png)

* Data ID 是 bootstrap.yml 配置的: `spring.cloud.sentinel.datasource.ds1.nacos.dataId`
* 配置信息中的: resource 和 @SentinelResource 的 value 保持一致

> 配置文件中的内容

```json
[
  {
    "resource": "protected-resource",
    "controlBehavior": 2,
    "count": 1,
    "grade": 1,
    "limitApp": "default",
    "strategy": 0
  }
]
```

> 配置文件说明

| 参数            | 含义                                  | 选项                   |
| --------------- | ------------------------------------- | ---------------------- |
| grade           | 限流阈值类型                          | 0 基于线程数 1 基于QPS |
| count           | 限流阈值                              |                        |
| controlBehavior | QPS流量控制中对超过阈值的流量处理手段 | 0 直接拒绝 - 1 Warm Up - 2 匀速排队 |
| strategy | 调用关系限流策略 | 0 根据调用方限流(limitApp) - 1 根据调用链路入口限流 - 2 具有关系的资源流量控制 |
| limitApp | 调用来源 | default 不区分调用者 {some_origin_name} 针对特定的调用者 other 针对除 {some_origin_name} 以外的其余调用方 |

> #### 接入 Sentinel控制台

> 去github 上下载控制台程序

```java
https://github.com/alibaba/Sentinel/releases
```

> 将jar包项目启动起来

```java
# -Dsentinel.dashboard.auth.username=sentinel 用于指定控制台的登录用户名为 sentinel；
# -Dsentinel.dashboard.auth.password=123456 用于指定控制台的登录密码为 123456；
# 如果省略这两个参数，默认用户和密码均为 sentinel；
java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```

> 在项目中添加配置指向

```yaml
spring:
  cloud:
    sentinel:
      eager: true
      transport:
        dashboard: localhost:8080
```

![](img/sentinel-console.png)

> #### 通过 StringGateway 实现 zuul 一样的路由转发

> 在 Gateway 项目添加相关依赖

```xml
<properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Greenwich.SR2</spring-cloud.version>
    <spring-cloud-alibaba.version>2.1.0.RELEASE</spring-cloud-alibaba.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>${spring-cloud-alibaba.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

> 在项目中添加配置与规则

```yaml
spring:
  application:
    name: gateway-server
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
      routes:
        - id: provider-server-route
          uri: lb://provider-server
          predicates:
            - Path=/provider/**
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
```

* 这里要注意的是 Gateway 和 zuul 有点不一样如果不了解可能会出现请求转发不过去
* zull 会省略前缀  Gateway 不会省略需要通过配置来省略前缀，也就是 `/被转发的项目名/**`

> 在路由时添加熔断机制

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

```yaml
spring:
  application:
    name: gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
      routes:
        - id: provider-server-route
          uri: lb://provider-server
          predicates:
            - Path=/provider/**
          filters:
            - StripPrefix=1 #去掉Path前缀,参数为1代表去掉/provider
            - name: Hystrix
              args:
                name: suibianqu
                fallbackUri: forward:/fallback
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
```

```java
@RestController
@SpringBootApplication
@EnableDiscoveryClient
public class ServerGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServerGatewayApplication.class, args);
    }

    @GetMapping("/fallback")
    public Map<String, String> fallback() {
        HashMap<String, String> smap = new HashMap<>();
        smap.put("code", "9999");
        smap.put("msg", "人数太拥挤请稍后重试");
        smap.put("data", "");
        return smap;
    }
}
```

> ### 配置动态路由

> 配置文件中

```yaml
spring:
  application:
    name: gateway
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
      discovery:
        server-addr: 127.0.0.1:8848
    gateway:
      discovery:
        locator:
          enabled: true
      default-filters:
        - name: Hystrix
          args:
            name : default
            fallbackUri: 'forward:/defaultFallback'

server:
  port: 1919
```

> 在服务启动时添加监听

```java
@Component
public class NacosDynamicRouteService implements ApplicationEventPublisherAware {

    private String dataId = "gateway-router";

    private String group = "DEFAULT_GROUP";

    @Value("${spring.cloud.nacos.config.server-addr}")
    private String serverAddr;

    @Autowired
    private RouteDefinitionWriter routeDefinitionWriter;

    private ApplicationEventPublisher applicationEventPublisher;

    private static final List<String> ROUTE_LIST = new ArrayList<>();

    @PostConstruct
    public void dynamicRouteByNacosListener() {
        try {
            ConfigService configService = NacosFactory.createConfigService(serverAddr);
            configService.getConfig(dataId, group, 5000);
            configService.addListener(dataId, group, new Listener() {
                @Override
                public void receiveConfigInfo(String configInfo) {
                    clearRoute();
                    try {
                        List<RouteDefinition> gatewayRouteDefinitions = JSONObject.parseArray(configInfo, RouteDefinition.class);
                        for (RouteDefinition routeDefinition : gatewayRouteDefinitions) {
                            addRoute(routeDefinition);
                        }
                        publish();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }

                @Override
                public Executor getExecutor() {
                    return null;
                }
            });
        } catch (NacosException e) {
            e.printStackTrace();
        }
    }

    private void clearRoute() {
        for(String id : ROUTE_LIST) {
            this.routeDefinitionWriter.delete(Mono.just(id)).subscribe();
        }
        ROUTE_LIST.clear();
    }

    private void addRoute(RouteDefinition definition) {
        try {
            routeDefinitionWriter.save(Mono.just(definition)).subscribe();
            ROUTE_LIST.add(definition.getId());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void publish() {
        this.applicationEventPublisher.publishEvent(new RefreshRoutesEvent(this.routeDefinitionWriter));
    }

    @Override
    public void setApplicationEventPublisher(ApplicationEventPublisher applicationEventPublisher) {
        this.applicationEventPublisher = applicationEventPublisher;
    }
}
```

> 在 nacos 控制台中添加配置

![](img/nacos-ui-config-router.png)

* Data ID:: 值为${spring.application.name}-router
* 配置格式选择 JSON

```json
[{
    "id": "baidu-router",
    "order": 0,
    "predicates": [{
        "args": {
            "pattern": "/du/**"
        },
        "name": "Path"
    }],
    "uri": "https://www.baidu.com"
},{
    "id": "provider-router",
    "order": 2,
    "predicates": [{
        "args": {
            "pattern": "/provider/**"
        },
        "name": "Path"
    }],
    "uri": "lb://provider-server"
}]
```

> #### 通过 Redis 令牌桶限流

> 导入 Redis 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
</dependency>
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

> 配置redis地址

```properties
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

> 在项目中添加两个配置类

```java
@Configuration
public class RedisConfiguration {
 
    @Bean("redisTemplate")
    public RedisTemplate redisTemplate(@Value("${spring.redis.host}") String host,
                                       @Value("${spring.redis.port}") int port) {
        RedisTemplate redisTemplate = new RedisTemplate();
        RedisSerializer stringRedisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        redisTemplate.setKeySerializer(stringRedisSerializer);
        redisTemplate.setHashKeySerializer(stringRedisSerializer);
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.setConnectionFactory(standaloneConnectionFactory(host, port));
        return redisTemplate;
    }
 
    protected JedisConnectionFactory standaloneConnectionFactory(String host, int port) {
        RedisStandaloneConfiguration redisStandaloneConfiguration = new RedisStandaloneConfiguration();
        redisStandaloneConfiguration.setHostName(host);
        redisStandaloneConfiguration.setPort(port);
        return new JedisConnectionFactory(redisStandaloneConfiguration);
    }
}
```

```java
@Configuration
public class RateLimiterConfiguration { 
    @Bean(value = "ipKeyResolver")
    public KeyResolver ipKeyResolver() {
        return exchange -> Mono.just(exchange.getRequest().getRemoteAddress().getAddress().getHostAddress());
    }
}
```

> 在路由中的 Filters 中添加桶的配置

```yaml
filters:
        - name: RequestRateLimiter
          args:
            redis-rate-limiter.replenishRate: 1
            redis-rate-limiter.burstCapacity: 5
            key-resolver: '#{@ipKeyResolver}'
```

> 自定义 Filter

```java
@Component
public class MyFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        System.out.println("line:1");
        return chain.filter(exchange).then(Mono.fromRunnable(() -> {
            System.out.println("line:2");
        }));
    }

    @Override
    public int getOrder() {
        return -1;
    }
}
```

