> ## 01-SpringCloud-NewHelloWorld.md

> #### 服务注册中心`register`

> `pom.xml` 引入

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

> `application.yaml` 中配置

```yaml
server:
  port: 8761
  
eureka:
  instance:
    hostname: localhost
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka
```

> 在`@SpringBootApplication`启动类中添加注解

```java
@EnableEurekaServer
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(SpringCloudCoreApplication.class, args);
	}

}
```

> #### 注册中心下的服务`server`

>  `pom.xml` 文件中添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

> `application.yaml` 中配置

```yaml
spring:
  application:
    name: client-server

server:
  port: 9999

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
    instance:
      lease-renewal-interval-in-seconds: 10
    register-with-eureka: true
```

> 在`@SpringBootApplication`启动类上添加注解

```java
@RestController
@EnableEurekaClient
@SpringBootApplication
public class SpringCloudServer1Application {

	@GetMapping("hello")
	public String hello() {
		return "Hello World!";
	}
	
	public static void main(String[] args) {
		SpringApplication.run(SpringCloudServer1Application.class, args);
	}

}
```



