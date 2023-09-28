> ## RabbitMQ SpringBoot 基础用法

> SpringBoot 项目导入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

> 配置 RabbitMQ 相关信息

```properties
spring.rabbitmq.host=192.168.3.18
spring.rabbitmq.port=5672
spring.rabbitmq.virtual-host=/dev_host

spring.rabbitmq.username=dev
spring.rabbitmq.password=123456
```

> 编写发送者`controllers`

```java
@RestController
@RequestMapping("/rabbitmq")
public class RabbitmqController {

    @Autowired
    private AmqpTemplate amqpTemplate;

    @GetMapping("/sean")
    public String sean () {
        amqpTemplate.convertAndSend("helloQueue", "NOW::" + LocalDateTime.now());
        return "success";
    }

}
```

> 编写`html`页面进行访问

```html
<a href="/rabbitmq/sean">sean</a>
```

> 编写消费者`RabbitListener`

```java
@Component
public class HelloRabbitmq {

    @RabbitListener(queuesToDeclare = @Queue("helloQueue"))
    public void hello(String message) {
        System.out.println("HelloRabbitmq.hello :: " + message);
    }

}
```

---

> #### @RabbitListener

> @RabbitListener(queues = "helloQueue") :: 监听一个已存在的队列

> @RabbitListener(queuesToDeclare = {@Queue("helloQueue")}) :: 监听一个队列如果不存在则创建

> 创建队列是顺便绑定`exchange`, 指定路由`key`
>
> ```java
> @RabbitListener(bindings = {@QueueBinding(
>     exchange = @Exchange("bindExchange"),
>     key = "key",
>     value = @Queue("queue")
> )}
> ```

> #### AmqpTemplate :: 指定路由`key`

```java
@GetMapping("/sean/{key}")
public String sean (@PathVariable("key") String routingKey) {
    amqpTemplate.convertAndSend("helloQueue", routingKey, "NOW::" + LocalDateTime.now());
    return "success";
}
```

