> ## springboot aop mongodb

> 导入 aop 与 mongodb 的 starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

> 配置 mongodb 数据源

```yaml
spring:
   data:
     mongodb:
       database: test
       host: localhost
       port: 27017
```

> 编写aop逻辑 并 将数据存入 mongodb

```java
@Aspect
@Component
public class SysAop {

    @Autowired
    private MongoTemplate mongoTemplate;

    @Around("execution(public * com.znsd.mongodb.service.*.*(..))")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        SystemLog log = new SystemLog();
        
		System.out.println(joinPoint.getSignature().getName());// 方法名
        System.out.println(Arrays.toString(joinPoint.getArgs()));// 方法入参
        
        MethodSignature ms = (MethodSignature) joinPoint.getSignature();
		
        System.out.println(ms.getMethod());// 当前执行的方法

        mongoTemplate.insert(log);
        return o;
    }

}
```

