> ##  spring-boot-starter-data-redis 注解的使用

> 需要导入 spring-boot-starter-data-redis

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

> 在 application.properties 文件中配置 redis 相关参数

```properties
# Redis数据库索引（默认为0）
spring.redis.database=0
# Redis服务器地址
spring.redis.host=localhost
# Redis服务器连接端口
spring.redis.port=6379
# Redis服务器连接密码（默认为空）
spring.redis.password=pwd
# 连接池最大连接数（使用负值表示没有限制）
spring.redis.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.pool.max-wait=-1
# 连接池中的最大空闲连接
spring.redis.pool.max-idle=8
# 连接池中的最小空闲连接
spring.redis.pool.min-idle=0
# 连接超时时间（毫秒）
spring.redis.timeout=0
```

> spring-boot-starter-data-redis 中带的注解

| 注解         | 作用                               |
| ------------ | ---------------------------------- |
| @Cacheable   | 方法执行前先检查一次缓存           |
| @CachePut    | 将方法的返回值加入缓存             |
| @CacheEvict  | 方法执行成功后删除缓存中相应的数据 |
| @CacheConfig | 统一指定缓存的名字                 |

> 伪代码

```java
@RestController("/cache")
@CacheConfig(cacheNames = "cache")
public class RedisCacheController {

    private static List<String> list = Arrays.asList("AA", "BB", "CC");

    @Cacheable
    @GetMapping("/")
    public List<String> list() {
        return list;
    }

    @PostMapping("/")
    @CacheEvict(allEntries = true)
    public List<String> add(String key) {
        list.add(key);
        return list();
    }
    
    @CachePut
    @DeleteMapping("/")
    public List<String> del(String key) {
        list.remove(key);
        return list();
    }

}
```

