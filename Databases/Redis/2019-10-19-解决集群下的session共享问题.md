# 解决集群下的session共享问题

### 1. 为什么会出现session共享问题

1. session是存储在服务器中的，所有在集群的情况下有多台服务器，所有不能够直接获取到非本服务器外的session

### 2. session共享解决翻案

1. 使用Spring-session + Redis
2. 将session存入数据库 （对数据库压力大）
3. 使用cookie（不安全）
4. tomcat配置session共享
5. token重写session

#### 2.1 使用spring-session+Redis

>  pom.xml

````xml
<!-- sptingboot 2.0版本 引入包 -->
<!--spring boot 与redis应用基本环境配置 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!--spring session 与redis应用基本环境配置,需要开启redis后才可以使用，不然启动Spring boot会报错 -->
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
</dependency>
<!--jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>

<!-- sptingboot 1.4版本一下 引入架包 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
</dependency>
````

>  config

```java
// 设置session过期时间
@EnableRedisHttpSession(maxInactiveIntervalInSeconds = 1800)
public class SessionConfig {

	// 冒号后的值为没有配置文件时，制动装载的默认值
	@Value("${redis.hostname:localhost}")
	String HostName;
	@Value("${redis.port:6379}")
	int Port;

	@Bean
	public JedisConnectionFactory connectionFactory() {
		JedisConnectionFactory connection = new JedisConnectionFactory();
		connection.setPort(Port);
		connection.setHostName(HostName);
		return connection;
	}
}
```

>  初始化session配置

```java
public class SessionInitializer extends AbstractHttpSessionApplicationInitializer {
	public SessionInitializer() {
		super(SessionConfig.class);
	}
}
```

>  配置文件

```properties
server.port=8080

spring.redis.database=0
spring.redis.host=192.168.110.185
spring.redis.port=6379
spring.redis.password=123456
spring.redis.pool.max-idle=8
spring.redis.pool.min-idle=0
spring.redis.pool.max-active=8
spring.redis.pool.max-wait=-1
spring.redis.timeout=5000


redis.hostname=192.168.110.185
redis.port=6379
redis.password=123456

```

>  构造器层

```java
@RestController
public class IndexController {

	@Value("${server.port}")
	private String port;
	
	@RequestMapping("/index")
	public String index() {
		
		return port;
	}
	
	@RequestMapping("/setSession")
	public String setSession(HttpServletRequest request, String sessionKey, String sessionValue) {
		HttpSession session = request.getSession(true);
		session.setAttribute(sessionKey, sessionValue);
		return "success,port:" + port+"\tsessionKey:"+sessionKey;
	}

	@RequestMapping("/getSession")
	public String getSession(HttpServletRequest request, String sessionKey) {
		HttpSession session =null;
		try {
		 session = request.getSession(false);
		} catch (Exception e) {
		  e.printStackTrace();
		}
		String value=null;
		if(session!=null){
			value = (String) session.getAttribute(sessionKey);
		}
		return "sessionValue:" + value + ",port:" + port+",sessionKey:"+sessionKey;
	}

}
```
