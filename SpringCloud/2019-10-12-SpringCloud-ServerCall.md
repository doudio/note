> ### SpringCloud ServerCall

> 服务之间的调用, 在`服务` `pom` 文件中添加配置

```xml
<!-- Feign依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```

> 配置 application.properties 文件

```properties
#端口
server.port=7072
#服务名称
spring.application.name=payServer
#是设置Eureka地址，服务启动将注册到这个Eureka上
eureka.client.serviceUrl.defaultZone=http://localhost:7070/eureka/
#实例名称在Eureka上面可以看到
eureka.instance.appname=payServer
```
> 在 SpringBoot 主程序中添加注解: `@EnableFeignClients`

```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
public class ConsumerApplication {

	public static void main(String[] args) {
		new SpringApplicationBuilder(ConsumerApplication.class).web(true).run(args);
	}

}
```

> 定义服务接口调用

```java
@Component
@FeignClient("server")
public interface ServiceCall {

	@RequestMapping("/hello")
	public String hello();

}
```

> `@FeignClient()` : `value` = 被调用的服务 ID :: `eureka.instance.appname`

> 直接注入接口进行使用

> 进行在`服务调用方`进行配置负载策略

```java
@Configuration
public class MySelfRule {

	@Bean
	public IRule myRule() {
		return new RandomRule();
	}

}
```

> 默认负载策略`轮巡`

| 类                        | 策略                                                         |
| ------------------------- | ------------------------------------------------------------ |
| RandomRule                | 随机                                                         |
| RoundRobinRule            | 按照线性轮询的方式依次选择每个服务实例                       |
| WeightedResponseTimeRule  | WeightedResponseTimeRule继承自RoundRobinRule，在选择服务实例的时候把权重因素也考虑进去。WeightedResponseTimeRule在初始化的时候会启动一个定时任务用来计算每个服务实例的权重，默认间隔为30秒。 |
| RetryRule                 | RetryRule实现了一个具备重试机制的实例选择功能                |
| BestAvailableRule         | 分配给最闲的服务                                             |
| PredicateBasedRule        | Predicate 是 Google Guava Collection 工具对集合进行过滤的条件接口 |
| AvailabilityFilteringRule | 基本处理逻辑也是“先过滤清单、再轮询选择”                     |
| ZoneAvoidanceRule         | 进行服务实例清单的过滤                                       |

> ### Feign 熔断降级

> 在服务调用端添加 pom 文件配置

```xml
<!-- 添加熔断器,提供服务熔断操作 -->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```

> 开启熔断降级

```properties
#开启hystrix熔断机制
feign.hystrix.enabled=true
```

> 编写 feign 调用类

```java
@FeignClient(value="server" ,fallback = MessageApiFailBack.class)
public interface MyServer {
    String hello ();
}
```

> 编写熔断回调类

```java
@Component
public class MessageApiFailBack implements MyServer {
	
    public String hello () {
		return "server error";
	}
    
}
```

> 定义熔断实现类 实现 `server` 接口 , 服务挂掉后放回熔断值

> #### 注意: 如果在开发中找不到注解可以导入

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-netflix-core</artifactId>
    <version>1.4.6.RELEASE</version>
</dependency>
```

> #### 注意: 服务之间调用
>
> **Required String parameter 'request' is not present**
>
> 找不到服务可能是: 被调用的服务还没有在注册中西里注册, 而此时调用方服务已经启动了!!!
>
> 也就是说: `Feign`提供服务方需要在调用方之前注册到服务中西!!!

```
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.

Tue Jan 08 20:51:13 CST 2019
There was an unexpected error (type=Bad Request, status=400).
Required String parameter 'request' is not present
```

