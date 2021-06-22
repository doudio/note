> ## SpringCloud Session Redis

> 1> 导入pom配置

```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-redis</artifactId>  
</dependency>
<dependency>  
    <groupId>org.springframework.session</groupId>  
    <artifactId>spring-session-data-redis</artifactId>  
</dependency>
```

> 2> 添加Reids连接

```yaml
spring: 
   redis: 
      host: localhost
      port: 6379
```

> 3> 在启动类上添加注解 :: @EnableRedisHttpSession

```java
@EnableEurekaClient
@SpringBootApplication
@EnableRedisHttpSession
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

> 4> 编写Controller处理请求

```java
@RestController
@RequestMapping("/session")
public class SessionController {

	@GetMapping("/attr")
	public String key(String key, String value, HttpSession session) {
		Object attribute = session.getAttribute(key);
		if (null == attribute) {
			session.setAttribute(key, value);
			return "not exists(" + key + "::" + value + ")";
		}
		return "exists(" + key + "::" + attribute + ")";
	}
	
}
```

