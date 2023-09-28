> ## 01-RocketMQ-hello-world.md

1. 下载安装包
   1. https://www.apache.org/dyn/closer.cgi?path=rocketmq/4.4.0/rocketmq-all-4.4.0-source-release.zip
2. 上传至centos
3. 解压压缩包

```shell
# 使用unzip解压zip压缩包
unzip rocketmq-all-4.4.0-source-release.zip
```

4. 编译安装

```shell
#进入解压后的目录
cd rocketmq-all-4.4.0/
# 使用maven进行编译安装
mvn -Prelease-all -DskipTests clean install -U
```

5. 启动rocketMQ

```shell
# 进入编译安装后的rocketmq根目录
cd rocketmq-all-4.4.0/distribution/target/apache-rocketmq
# 首先后台启动名称服务器namasrv
nohup sh bin/mqnamesrv &
# 后台启动broker
nohup sh bin/mqbroker -n nameserver-ip:9876 &
# 设置环境变量
export NAMESRV_ADDR=localhost-ip:9876
# 关闭服务器
sh bin/mqshutdown broker
sh bin/mqshutdown namesrv
```

* ##### 若启动不起来泽 :: 设置broker的堆内存

```shell
# 进入rocketmq的bin目录
cd rocketmq-all-4.4.0/distribution/target/apache-rocketmq/bin
# 编辑runbroker.sh
vim runbroker.sh
# 修改堆内存：堆内存最少设置为1g
JAVA_OPT="${JAVA_OPT} -server -Xms2g -Xmx2g -Xmn1g"

###

# 编辑runserver.sh
#修改名称服务器的堆内存
vim runserver.sh
#修改堆内存
JAVA_OPT="${JAVA_OPT} -server -Xms2g -Xmx2g -Xmn1g"
```

> ### RocketMQ In Java

##### Pom.xml

```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-client</artifactId>
    <version>4.4.0</version>
</dependency>
```

##### Producer

```java
public static void main(String[] args) throws Exception {
    //声明消息生产者
    DefaultMQProducer producer = new DefaultMQProducer("producer_group");
    // 设置RocketMQ名称服务器地址
    producer.setNamesrvAddr("192.168.3.236:9876");
    producer.start();
    for (int i = 0; i < 100; i++) {
        // 定义消息内容：Topic、Tag、Body
        Message msg = new Message("loyalTest" ,
                                  "Tag1",
                                  ("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET)
                                 );
        //生产者发送消息至服务器
        SendResult sendResult = producer.send(msg);
        System.out.printf("%s%n", sendResult);
    }
    //关闭
    producer.shutdown();
}
```

##### Consumer

```java
public static void main(String[] args) throws InterruptedException, MQClientException {

    // 定义消费者实例
    DefaultMQPushConsumer consumer = newDefaultMQPushConsumer("consumer_group");

    // 配置名称服务器地址
    consumer.setNamesrvAddr("192.168.3.236:9876");

    // 设置消费的Topic及分类标识Tag1
    consumer.subscribe("loyalTest", "Tag1");
    consumer.registerMessageListener(new MessageListenerConcurrently() {
        @Override
        public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,
                                                        ConsumeConcurrentlyContext context) {
            MessageExt ext = msgs.get(0);
            try {
                String msg = new String(ext.getBody(),"UTF-8");
                System.out.println("Message==="+msg);
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        }
    });
    //开启消费者，开始进行消费.
    consumer.start();
    System.out.printf("Consumer Started.%n");
}
```