> # Redis 发布订阅

* shell 相关教学 https://www.runoob.com/redis/redis-pub-sub.html

> SpringBoot 操作使用

* #### 添加 Redis Starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

* #### 创建 Redis 配置文件

```java
@Component
public class RedisListenerConfig {

    /**
     * 配置 Redis 发布订阅
     * 需要监听多个 topic 时创建 MessageListenerAdapter 子类注入到配置方法中, 调用 addMessageListener 方法就好了
     */
    @Bean
    RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory, RunoobChatListenerAdapter runoobChatListenerAdapter) {
        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);

        // todo 配置监听 绑定 MessageListenerAdapter 的子类 与 PatternTopic
        container.addMessageListener(runoobChatListenerAdapter, new PatternTopic("runoobChat"));

        System.out.println("Subscribed Redis channel: runoobChat");
        return container;
    }

}
```

* #### 创建好 MessageListenerAdapter 处理类

```java
/**
 * redis 发布订阅 runoobChat 消息处理类
 */
@Component
public class RunoobChatListenerAdapter extends MessageListenerAdapter {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void onMessage(Message message, byte[] pattern) {
        String key = stringRedisTemplate.getStringSerializer().deserialize(pattern);
        String data = stringRedisTemplate.getStringSerializer().deserialize(message.getBody());

        System.out.println(String.format("runoobChat -> key: %s, data: %s", key, data));
    }

}
```

* #### 创建一个 Controller 测试发布消息到 runoobChat

```java
@RestController
public class PupController {

    @Autowired
    private StringRedisTemplate redisTemplate;

    @GetMapping("pub")
    public String pub(String key, String val) {
        redisTemplate.convertAndSend(key, val);
        return "SUCCESS";
    }

}
```

* #### 测试运行效果

```shell
Subscribed Redis channel: runoobChat
2022-04-20 16:33:09.345  INFO 21136 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8011 (http) with context path ''
2022-04-20 16:33:10.067  INFO 21136 --- [           main] c.e.redispubsub.RedisPubSubApplication   : Started RedisPubSubApplication in 3.196 seconds (JVM running for 4.545)
2022-04-20 16:33:25.383  INFO 21136 --- [nio-8011-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2022-04-20 16:33:25.383  INFO 21136 --- [nio-8011-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
2022-04-20 16:33:25.385  INFO 21136 --- [nio-8011-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 2 ms
runoobChat -> key: runoobChat, data: testVal
runoobChat -> key: runoobChat, data: testVal1
runoobChat -> key: runoobChat, data: testVal2
runoobChat -> key: runoobChat, data: testVal3
```

* #### 客户端指令

```shell
SUBSCRIBE runoobChat
PUBLISH runoobChat "Learn redis by runoob.com"

序号	命令及描述
1	PSUBSCRIBE pattern [pattern ...] 订阅一个或多个符合给定模式的频道。
2	PUBSUB subcommand [argument [argument ...]] 查看订阅与发布系统状态。
3	PUBLISH channel message 将信息发送到指定的频道。
4	PUNSUBSCRIBE [pattern [pattern ...]] 退订所有给定模式的频道。
5	SUBSCRIBE channel [channel ...] 订阅给定的一个或多个频道的信息。
6	UNSUBSCRIBE [channel [channel ...]] 指退订给定的频道。
```