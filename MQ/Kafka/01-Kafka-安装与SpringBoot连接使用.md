> ## Kafka-安装与SpringBoot连接使用

> 在安装 Kafka 时我们先给 ZooKeeper 安装跑起来

* 下载地址: https://zookeeper.apache.org/releases.html

```shell
wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.14/zookeeper-3.4.14.tar.gz
```

> 解压并到zookeeper目录中

```shell
tar -zxvf zookeeper-3.4.14.tar.gz
cd zookeeper-3.4.14
```

> 将 zookeeper 提供的简单实例备份成: `zoo.cfg`

```shell
cp conf/zoo_sample.cfg conf/zoo.cfg 
```

> 单机模式可以使用默认配置集群模式需要修改部分配置

```shell
vim conf/zoo.cfg

# 心跳时间, 时长单位为毫秒
tickTime=2000
# 存储快照文件的目录
dataDir=/tmp/zookeeper
# zookeeper的TCP端口
clientPort=2181
# follower与leader同步的最长时间
initLimit=5
# follower和leader之间响应的最长时间
syncLimit=2

#server.A=B：C：D：其中 A 数字，表示是第几号服务器. dataDir目录下必有一个myid文件，里面只存储A的值,ZK启动时读取此文件，与下面列表比较判断是哪个server
# B 是服务器 ip ；C表示与 Leader 服务器交换信息的端口；D 表示的是进行选举时的通信端口。
server.1=<ip>:<port>:<port>
server.2=<ip>:<port>:<port>
server.3=<ip>:<port>:<port>
```

> 启动 zookeeper

```shell
# 启动 zookeeper
./bin/zkServer.sh start
# 可查看日志输出
./bin/zkServer.sh start-foreground
# 连接到 zookeeper 集群可以 , 分割
./bin/zkCli.sh -server <ip>:<zk-port>
```

> 安装Kafka

* 下载地址: https://kafka.apache.org/downloads

```shell
wget http://mirrors.shuosc.org/apache/kafka/1.0.0/kafka_2.11-1.0.0.tgz
```

> 解压并修改部分配置

```shell
tar -zxvf kafka_2.11-1.0.0.tgz
cd kafka_2.11-1.0.0
vim config/server.properties

broker.id=1
listeners=PLAINTEXT://:<default-port:9092>
advertised.listeners=PLAINTEXT://<ip>:<default-port:9092>
log.dirs=/tmp/kafka-logs

# 在kafka中执行 zookeeper 指令
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
# 启动kafka服务
bin/kafka-server-start.sh config/server.properties

# 创建 topic
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic devtopic
# 查询 topic
bin/kafka-topics.sh --list --zookeeper localhost:2181
# 查看 topic 信息
bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic devtopic

# shell 端生产者
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic devtopic
# shell 端消费者
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic devtopic
```

> 本文基于原作者: https://blog.csdn.net/u010889616/article/details/80641922

> 在 Spring Boot 中使用 kafka

> 生产者和消费者都导入 spring-kafka

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

> 生产者配置

```properties
spring.kafka.producer.bootstrap-servers=<ip>:<zk-port>
spring.kafka.producer.retries=1
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

com.kafka.kafkacomponent.topic=devtopic
```

> 编写 kafka 发送消息的组件

```java
@Component
public class KafkaComponent {

    @Value("${com.kafka.kafkacomponent.topic}")
    private String topic;

    @Autowired
    private KafkaTemplate<String, Object> kafkaTemplate;

    public void send(String body) {
        kafkaTemplate.send(topic, body);
    }

}
```

> 编写 Controller 发送消息

```java
@RestController
@SpringBootApplication
public class SpringbootKafkaProducerApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootKafkaProducerApplication.class, args);
    }

    @Autowired
    private KafkaComponent kafkaComponent;

    @GetMapping("kafka")
    public void kafka(String data) {
        kafkaComponent.send(data);
    }

}
```

> 消费者配置

```properties
spring.kafka.consumer.bootstrap-servers=<ip>:<zk-port>
spring.kafka.consumer.group-id=0
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.enable-auto-commit=true
spring.kafka.consumer.auto-commit-interval=100
#=======set comsumer max fetch.byte 2*1024*1024=============
spring.kafka.consumer.properties.max.partition.fetch.bytes=2097152

com.kafka.kafkaconsumerlistener.topic=devtopic
com.kafka.kafkaconsumerlistener.groupId=devtopic-groupid

# mySecret:81704954bb3ac8798573011b67ecc4a4
my.secret=${random.value}
# myNumber:-167189538
my.number=${random.int}
# myBignumber:-3193751453883786185
my.bignumber=${random.long}
# myUuid:wanren-api-0c1acae5-fd0d-4dbf-8c12-3e54d1782851
my.uuid=${random.uuid}
# myNumberLessThanTen:2
my.number.less.than.ten=${random.int(10)}
# myNumberInRange:49940
my.number.in.range=${random.int[1024,65536]}
```

> 创建方法监听

```java
@Component
public class KafkaConsumerListener {

    @KafkaListener(topics = { "${com.kafka.kafkaconsumerlistener.topic}" })
    public void listen(ConsumerRecord<?, ?> record) {
        System.out.println(record.value());
    }

    @KafkaListener(topics = { "${com.kafka.kafkaconsumerlistener.topic}" }, groupId = "${com.kafka.kafkaconsumerlistener.groupId}")
    public void listen(ConsumerRecord<?, ?> record) {
        System.out.println(record.value());
    }

}
```

* `topic` :: 监听的主题。
* `groupId` :: 在主题相同, 组ID相同时消息模式是 *点对点* 的 , 主题相同, 组ID不同时消息的模式是 *发布订阅*。

