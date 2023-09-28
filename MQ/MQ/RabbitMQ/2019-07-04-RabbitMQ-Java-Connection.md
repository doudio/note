> ## RabbitMQ Java Connection

![](img/RabbitMQ-one.png)

> P::消息的生产者
>
> C::消息的消费者
>
> 红色:：队列

> 创建maven项目`并`添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

> 编写 : 连接 : 工具 : `AmqpUtils.java`

```java
/**
 * 获取RabbitMQ连接对象
 * @return
 */
public static Connection getConnection() throws Exception {
    ConnectionFactory factory = new ConnectionFactory();
    // 设置服务器地址
    factory.setHost("localhost");
    // 设置服务器用户名
    factory.setUsername("dev_user");
    // 设置服务器密码
    factory.setPassword("password");
    // 设置服务器端口
    factory.setPort(5672);
    // 设置虚拟名称
    factory.setVirtualHost("/deb_host");

    // 创建连接对象
	return factory.newConnection();
}
```

> 创建 :: 生产者

```java
public class Production {

    public static final String TEST_SIMPLE_QUEUE = "test_simple_queue";

    public static void main(String[] args) throws Exception {

        // 获取一个连接对象
        Connection connection = AmqpUtils.getConnection();

        // 从连接对象中获取一个连接渠道
        Channel channel = connection.createChannel();

        // 声明一个队列
        channel.queueDeclare(TEST_SIMPLE_QUEUE, false, false, false, null);

        // 需要发送的消息
        String msg = "hello world rabbitmq";

        // 第一个参数是exchangeName(默认情况下代理服务器端是存在一个""名字的exchange的,
        // 因此如果不创建exchange的话我们可以直接将该参数设置成"",如果创建了exchange的话
        // 我们需要将该参数设置成创建的exchange的名字),第二个参数是路由键
        channel.basicPublish("", TEST_SIMPLE_QUEUE, null, msg.getBytes());

        // 关闭连接对象
        channel.close();
        connection.close();
        System.out.println("SUCCESS");
    }

}
```

> 创建 :: 消费者

```java
public class Consumption {

    public static final String TEST_SIMPLE_QUEUE = "test_simple_queue";

    public static void main(String[] args) throws Exception {
        // 创建连接
        Connection connection = AmqpUtils.getConnection();

        // 创建频道
        Channel channel = connection.createChannel();

        // 定义消费者
        Consumer consumer = new DefaultConsumer(channel) {
            // 获取消息的方法
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body)
                    throws IOException {
                String message = new String(body, "UTF-8");
                System.out.println(" [x] Received '" + message + "'");
            }
        };
        // 监听队列
        channel.basicConsume(TEST_SIMPLE_QUEUE, true, consumer);
    }

}
```

