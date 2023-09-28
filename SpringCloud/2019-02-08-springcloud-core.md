# Spring Cloud

#### 什么是微服务（Microservice）

　　微服务英文名称Microservice，Microservice架构模式就是将整个Web应用组织为一系列小的Web服务。这些小的Web服务可以独立地编译及部署，并通过各自暴露的API接口相互通讯。它们彼此相互协作，作为一个整体为用户提供功能，却可以独立地进行扩。

#### 微服务架构需要的功能或使用场景

1：我们把整个系统根据业务拆分成几个子系统。

2：每个子系统可以部署多个应用，多个应用之间使用负载均衡。

3：需要一个服务注册中心，所有的服务都在注册中心注册，负载均衡也是通过在注册中心注册的服务来使用一定策略来实现。

4：所有的客户端都通过同一个网关地址访问后台的服务，通过路由配置，网关来判断一个URL请求由哪个服务处理。请求转发到服务上的时候也使用负载均衡。

5：服务之间有时候也需要相互访问。例如有一个用户模块，其他服务在处理一些业务的时候，要获取用户服务的用户数据。

6：需要一个断路器，及时处理服务调用时的超时和错误，防止由于其中一个服务的问题而导致整体系统的瘫痪。

7：还需要一个监控功能，监控每个服务调用花费的时间等。

目前主流的微服务框架：Dubbo、 SpringCloud、thrift、Hessian等，目前国内的中小企业用的大多数都是Dubbo，SpringCloud。

## SpringCloud简介

　　SpringCloud是基于SpringBoot的一整套实现微服务的框架。他提供了微服务开发所需的配置管理、服务发现、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、分布式会话和集群状态管理等组件。最重要的是，  跟spring boot框架一起使用的话，会让你开发微服务架构的云服务非常好的方便。SpringBoot旨在简化创建产品级的 Spring 应用和服务，简化了配置文件，使用嵌入式web服务器，含有诸多开箱即用微服务功能。

相关组件架构图:

![](img/181.jpg)

#### spring cloud子项目包括：

**Spring Cloud Config**：配置管理开发工具包，可以让你把配置放到远程服务器，目前支持本地存储、Git以及Subversion。

**Spring Cloud Bus**：事件、消息总线，用于在集群（例如，配置变化事件）中传播状态变化，可与Spring Cloud Config联合实现热部署。 

 **Spring Cloud Netflix**：针对多种Netflix组件提供的开发工具包，其中包括Eureka、Hystrix、Zuul、Archaius等。 

 **Netflix Eureka**：云端负载均衡，一个基于 REST 的服务，用于定位服务，以实现云端的负载均衡和中间层服务器的故障转移。 

  **Netflix Hystrix**：容错管理工具，旨在通过控制服务和第三方库的节点,从而对延迟和故障提供更强大的容错能力。 

 **Netflix Zuul**：边缘服务工具，是提供动态路由，监控，弹性，安全等的边缘服务。 

 **Netflix Archaius**：配置管理API，包含一系列配置管理API，提供动态类型化属性、线程安全配置操作、轮询框架、回调机制等功能。 

 **Spring Cloud for Cloud Foundry**：通过Oauth2协议绑定服务到CloudFoundry，CloudFoundry是VMware推出的开源PaaS云平台。 

 **Spring Cloud Sleuth**：日志收集工具包，封装了Dapper,Zipkin和HTrace操作。

 **Spring Cloud Data Flow**：大数据操作工具，通过命令行方式操作数据流。

 **Spring Cloud Security**：安全工具包，为你的应用程序添加安全控制，主要是指OAuth2。

 **Spring Cloud Consul**：封装了Consul操作，consul是一个服务发现与配置工具，与Docker容器可以无缝集成。

 **Spring Cloud Zookeeper**：操作Zookeeper的工具包，用于使用zookeeper方式的服务注册和发现。

 **Spring Cloud Stream**：数据流操作开发包，封装了与Redis,Rabbit、Kafka等发送接收消息。

 **Spring Cloud CLI**：基于 Spring Boot CLI，可以让你以命令行方式快速建立云组件。



#### SpringCloud特点

1：约定优于配置

2：开箱即用、快速启动

3：适用于各种环境

4：轻量级的组件

5：组件支持丰富，功能齐全

## Spring Cloud Eureka

### 简介

　　Eureka是Netflix开发的服务发现框架，是Spring Cloud Netflix的一个子模块，也是核心模块之一。本身是一个基于REST的服务，主要用于定位运行在AWS域中的中间层服务，以达到负载均衡和中间层服务故障转移的目的。SpringCloud将它集成在其子项目spring-cloud-netflix中，以实现SpringCloud的服务发现功能。

　　Eureka包含两个组件：Eureka Server和Eureka Client。

　　Eureka Server提供服务注册服务，各个节点启动后，会在Eureka Server中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。

　　Eureka Client是一个java客户端，用于简化与Eureka Server的交互，客户端同时也就是一个内置的、使用轮询(round-robin)负载算法的负载均衡器。

　　在应用启动后，将会向Eureka Server发送心跳,默认周期为30秒，如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，Eureka Server将会从服务注册表中把这个服务节点移除(默认90秒)。

　　Eureka Server之间通过复制的方式完成数据的同步，Eureka还提供了客户端缓存机制，即使所有的Eureka Server都挂掉，客户端依然可以利用缓存中的信息消费其他服务的API。综上，Eureka通过心跳检查、客户端缓存等机制，确保了系统的高可用性、灵活性和可伸缩性。

下图是基本的服务注册和发现

![](img/eureka.png)

### Eureka实例

#### 创建Eureka服务端步骤

1. 创建maven项目
2. 配置依赖
3. 写程序入口
4. 配置application.properties文件
5. 启动服务

##### 创建maven项目加入Eureka依赖

1、配置Spring Cloud依赖

```xml
<!-- Spring Cloud支撑包 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-parent</artifactId>
            <version>Edgware.SR3</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

2、配置Eureka依赖

```xml
<!-- Eureka依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
<!-- eureka服务依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka-server</artifactId>
</dependency>
```

3、配置web依赖

```xml
<!--web项目依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

4、插件配置

```xml
<build>
    <!-- 打包项目名称 -->
    <finalName>springboot-eureka-server</finalName>
    <!-- 打包插件 -->
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

##### 主程序入口

1、编写主程序

```java
// 开启eureka服务注册中心
@EnableEurekaServer
// 表示为springboot应用程序
@SpringBootApplication
public class EurekaRegisterCenterApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaRegisterCenterApplication.class, args);
	}
}
```

2、配置z.properties文件

```properties
#端口
server.port=7070
#应用名称
spring.application.name= server‐01
#实例所在地址
eureka.instance.hostname=localhost
#是设置eureka服务器所在的地址，查询服务和注册服务都需要依赖这个地址
eureka.client.serviceUrl.defaultZone=http://localhost:7070/eureka/
#registerWithEureka表示是否注册自身到eureka服务器，因为当前这个应用就是eureka服务器，没必要注册自身，所以这里是false
eureka.client.registerWithEureka=false
#fetchRegistry表示是否从eureka服务器获取注册信息
eureka.client.fetchRegistry=false
```

3、启动Eureka服务，Eureka启动方式和Spring Boot一致，直接运行EurekaRegisterCenterApplication中的main方法即可启动Eureka服务，在浏览器输入http:// IP地址:端口/，可以访问Eureka服务页面，我这里是ip地址是localhost，端口号配置的是7070，所以访问地址为http://127.0.0.1:7070/

![](img/20180821211618.png)

#### 创建Eureka客户端

1. 创建maven项目
2. 配置依赖
3. 写客户端入口
4. 配置application.properties文件
5. 启动服务提供者客户端

##### 创建maven项目加入Eureka依赖

1、配置Spring Cloud依赖

```xml
<!-- Spring Cloud支撑包 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-parent</artifactId>
            <version>Edgware.SR3</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

2、配置Eureka依赖

```xml
<!-- 配置Eureka依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

3、配置web依赖

```xml
<!-- 配置web依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

4、插件配置

```xml
<build>
    <!-- 打包插件 -->
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

##### 编写Eureka客户端主程序

1、主程序入口

```java
//开启Spring Boot
@SpringBootApplication
//开启Eureka配置，将此服务注册到eureka注册中心
@EnableEurekaClient
// rest控制器
@RestController
public class UserServiceApplication {

	@RequestMapping("/say")
	public String say() {
		return "hello world!!";
	}
	
	public static void main(String[] args) {
		new SpringApplicationBuilder(UserServiceApplication.class).web(true).run(args);
	}
}
```

2、配置application.properties文件

```properties
#端口
server.port=7071
## 项目名称
server.context-path=/userServer
#服务名称
spring.application.name=userserver
#是设置Eureka地址，服务启动将注册到这个Eureka上面
eureka.client.serviceUrl.defaultZone=http://localhost:7070/eureka/
#实例名称在Eureka上面可以看到
eureka.instance.appname=userServer
```

3、启动Eureka服务提供者客户端

Eureka客户端启动方式和Spring Boot一致，启动后客户端服务就可以注册到Eureka上。可以参考服务端图片。`注意：`起客户端之前先启动Eureka服务

![](img/20180821213551.png)

### Eureka自我保护机制

　　Eureka是开启自我保护的，如果我们将注册到eureka的服务关闭服务关闭，我们会发现，我们除了看到了一行红色的警告信息，还发现了一件神奇的事情，就是我们的服务实例虽然被kill了，但是在服务注册中心他还是存在的。这就是Eureka自我保护机制，他不会剔除已经挂掉的服务，他会认为这个服务是在尝试重新连接的。
我们在开发过程中肯定是不希望这样的，不利于开发。我们可以关闭Eureka的自我保护机制（实际生产环境不建议关闭）。

#### 关闭自我保护机制

##### eureka客户端配置

```properties
#eureka客户端需要多长时间发送心跳给eureka服务器，表明他仍然或者，默认30秒
eureka.instance.lease-expiration-duration-in-seconds=20
#eureka服务器在接受到实力的最后一次发出的心跳后，需要等待多久才可以将此实力删除
eureka.instance.lease-renewal-interval-in-seconds=10
```

##### eureka服务端配置

```properties
#开启自我保护模式
eureka.server.enable-self-preservation=false
#清理无效节点,默认60*1000毫秒,即60秒
eureka.server.eviction-interval-timer-in-ms=5000
```

### Eureka身份认证

#### 配置security依赖

在eureka服务端配置如下约束

```xml
<!-- 注册服务安全 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

#### 配置服务

```properties
#开启安全机制设置用户名密码
security.basic.enabled=true
security.user.name=admin
security.user.password=123456
#服务注册地址例子如下
eureka.client.serviceUrl.defaultZone=http://admin:123456@localhost:7070/eureka/
```

### Eureka健康检查

　　人会生病，就像人一样服务有时候也会出现异常情况，我们也需要知道某个服务的健康状况。Eureka有健康检查机制。

我们可以通过http://localhost:7070/info查看服务信息；可以通过http://localhost:7070/health查看健康状况。

#### 配置健康检查

　　在程序运行过程中，eureka并不能根据你的意向去判断服务是否健康。因此eureka提供出自定义健康检查机制，让程序猿根据自己的意识或者说是业务，来判断服务是否健康。为此eureka提供出了两个接口：HealthIndicator（健康指示器）用来感知服务的状态，HealthCheckHandler（健康检查处理器）用来处理健康结果。

##### 在Eureka客户端加上如下依赖:

```xml
<!--健康检查依赖-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

##### 在客户端编写自定义检查逻辑

```java
@Component
public class MyHealthIndicator implements HealthIndicator{
	public Health health() {
		System.out.println(StudentApplication.canVisitDb);
        if(StudentApplication.canVisitDb) {
            return new Health.Builder(Status.UP).build();
        } else {
            return new Health.Builder(Status.DOWN).build();
        }
    }
}
```

##### 加快更新服务信息

```properties
#更新实例信息的变化到Eureka服务端的间隔时间，单位为秒,默认为30秒
#eureka.client.instanceInfoReplicationIntervalSeconds=10
```

##### 健康信息显示到eureka

````java
@Component
public class MyHealthCheckHandler implements HealthCheckHandler {
    @Autowired
    private MyHealthIndicator halthIndicator;
    public InstanceStatus getStatus(InstanceStatus currentStatus) {
        Status status = halthIndicator.health().getStatus();
        if(status.equals(Status.UP)) {
            return InstanceStatus.UP;
        } else {
            return InstanceStatus.DOWN;
        }
    }
}
````



## Spring Cloud Feign

### 简介

　　Feign是简化Java HTTP客户端开发的工具（java-to-httpclient-binder），它的灵感来自于Retrofit、JAXRS-2.0和WebSocket。Feign的初衷是降低统一绑定Denominator到HTTP API的复杂度，不区分是否为restful。

　　Feign包含了Ribbon和Hystrix，这个在实战中才慢慢体会到它的意义，所谓的包含并不是Feign的jar包包含有Ribbon和Hystrix的jar包这种物理上的包含，而是Feign的功能包含了其他两者的功能这种逻辑上的包含。简言之：Feign能干Ribbon和Hystrix的事情，但是要用Ribbon和Hystrix自带的注解必须要引入相应的jar包才可以。

　　SpringCloudFeign在NetFixFeign的基础上扩展了对SpringMVC注解的支持，在其实现下，我们只需创建一个接口并用注解的方式来配置它，即可完成对服务提供方的接口绑定。简化了SpringCloudRibbon自行封装服务调用客户端的开发量。

### Feign服务调用实例

#### 创建Feign客户端消费者步骤

1. 创建maven项目
2. 配置依赖
3. 写客户端入口
4. 写服务接口
5. 写接口调用类
6. 配置application.properties文件
7. 启动Feign客户端

##### 创建maven项目配置依赖

1、在Eureka客户端的基础上加上Feign依赖即可

```xml
<!-- Feign依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```

2、服务接口调用定义

```java
// 交给spring ioc容器
@Component
// springboot-eureka-server-provider项目中的application.properties文件中spring.application.name的值
//参数根据被调用的“spring.application.name=userserver”配置而来
@FeignClient("userserver")
public interface ConsumerService {

	/**
	 * /userServer=服务器共者项目中定义的项目名称（server.context-path）
	 * /say=服务提供者项目中的UserServiceApplication类中的@RequestMapping("/say")值
	 * 注意：接口申明必须和被调用接口保持一致（地址、接口名、参数、 返回值）
	 * @return
	 */
	@RequestMapping("/userServer/say")
	public String say();
}
```

3、将ConsumerService注入给UserController通过feign调用服务提供者的接口方法

```java
@RestController
public class UserController {

	@Autowired
	private ConsumerService consumerService;
	
	@RequestMapping("/testCallProviderByFeign")
	public String testCallProviderByFeign() {
		
		System.out.println("UserController.testCallProviderByFeign()");
		
		// 调用服务提供者的方法获取结果
		String result = this.consumerService.say();
		System.out.println("获取服务提供者的结果：" + result);
		
		return result;
	}
}
```

4、配置application.properties文件

```properties
#端口
server.port=7072
#服务地址
server.contextPath=/consumer
#服务名称
spring.application.name=consumerserver
#是设置Eureka地址，服务启动将注册到这个Eureka上
eureka.client.serviceUrl.defaultZone=http://localhost:7070/eureka/
#实例名称在Eureka上面可以看到
eureka.instance.appname=consumerserver
```

##### 编写客户端启动程序入口

```java
//开启Spring Boot
@SpringBootApplication
//开启Eureka配置
@EnableEurekaClient
//开启Feign
@EnableFeignClients
//rest控制器
@RestController
public class ConsumerApplication {

	public static void main(String[] args) {
		new SpringApplicationBuilder(ConsumerApplication.class).web(true).run(args);
	}
}
```

##### 启动Feign客户端

Eureka客户端启动方式和Spring Boot一致，启动后Feign客户端服务就可以注册到Eureka上。

![](img/20180821220909.png)

##### 测试发送请求

消费项目中的application.properties配置文件中的端口号=7072项目名=/consumer，所以请求地址为http://127.0.0.1:7072/consumer/testCallProviderByFeign

![](img/20180821220626.png)

![](img/20180821220652.png)



### Feign负载均衡

当我们使用Feign做负载均衡的时候，SpringCloud 整合了Ribbon和Eureka 去提供负载均衡。

#### 负载均衡实现

1、一个eureka服务注册中心服务，端口7070

2、一个消费者（调用者）服务，端口7071

3、两个生产（被调用者）服务，端口7072、7073；注意两个服务除了端口不一样，其余配置保持一样（如同部署了两个项目在两个不同的tomcat一样）。

完成上面3个步骤就已经完成了微服务内部负载均衡。亲，你可以进行测试了。



#### 更换默认负载均衡策略

在消费者（Feign使用服务的服务）加上如下配置。

```java
@Configuration
public class MySelfRule {
	@Bean
	public IRule myRule() {
		return new RandomRule();
	}
}
```

### Feign熔断降级

#### 熔断

假设一个请求的调用链上面有10个服务，只要这10个服务中有1个超时，就会导致这个请求超时。 
更严重的，如果该请求的并发数很高，所有该请求在短时间内都被block（等待超时），tomcat的所有线程都block在此请求上，导致其他请求没办法及时响应。为了解决上述问题，服务熔断的思想被提出来。类似现实世界中的“保险丝“，当某个异常条件被触发，直接熔断整个服务，而不是一直等到此服务超时。

#### 降级

有了熔断，就得有降级。所谓降级，就是当某个服务熔断之后，服务器将不再被调用，此时客户端可以自己准备一个本地的fallback回调，返回一个缺省值。 
这样做，虽然服务水平下降，但好歹可用，比直接挂掉要强，当然这也要看适合的业务场景。

#### 熔断降级实现

##### 1、在Feign服务（消费者）加入依赖

```xml
<!-- 添加熔断器,提供服务熔断操作 -->
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```

##### 2、开启熔断机制

```properties
#开启hystrix熔断机制
feign.hystrix.enabled=true
```



##### 3、熔断回调类

```
@Component
public class MessageApiFailBack implements ConsumerService {
	public String say() {
		return "亲，挂求了！";
	}
}
```

##### 4、Feign接口配置稍作修改

```java
//交给spring ioc容器
@Component
//springboot-eureka-server-provider项目中的application.properties文件中spring.application.name的值
//参数根据被调用的“spring.application.name=userserver”配置而来
@FeignClient(value="studentserver2" ,fallback = MessageApiFailBack.class)
public interface ConsumerService {

	/**
	 * /userServer=服务器共者项目中定义的项目名称（server.context-path）
	 * /say=服务提供者项目中的UserServiceApplication类中的@RequestMapping("/say")值
	 * 注意：接口申明必须和被调用接口保持一致（地址、接口名、参数、 返回值）
	 * @return
	 */
	@RequestMapping("/say")
	public String say();
}
```

##### 5、测试，关闭所有生产者服务。



## Spring Cloud Zuul

### 简介

　　Zuul 是Netflix 提供的一个开源组件，也是核心组件，致力于在云平台上提供动态路由，监控，弹性，安全等边缘网关服务的框架。其功能很多，比如反向代理，负载均衡还有权限控制等功能。

#### 为什么需要网关呢？

　　我们知道我们要进入一个服务本身，很明显我们没有特别好的办法，直接输入IP地址+端口号，我们知道这样的做法很糟糕的，这样的做法大有问题，首先暴露了我们实体机器的IP地址，别人一看你的IP地址就知道服务部署在哪里，让别人很方便的进行攻击操作。

　　第二，我们这么多服务，我们是不是要挨个调用它呀，我们这里假设做了个权限认证，我们每一个客户访问的都是跑在不同机器上的不同的JVM上的服务程序，我们每一个服务都需要一个服务认证，这样做烦不烦呀，明显是很烦的。那么我们这时候面临着这两个极其重要的问题，这时我们就需要一个办法解决它们。首先，我们看IP地址的暴露和IP地址写死后带来的单点问题，我是不是对这么服务本身我也要动态的维护它服务的列表呀，我需要调用这服务本身，是不是也要一个负载均衡一样的玩意，还有关于IP地址暴露的玩意，我是不是需要做一个代理呀，像Nginx的反向代理一样的东西，还有这玩意上部署公共的模块，比如所有入口的权限校验的东西。因此我们现在需要Zuul API网关。它就解决了上面的问题，你想调用某个服务，它会给你映射，把你服务的IP地址映射成某个路径，你输入该路径，它匹配到了，它就去替你访问这个服务，它会有个请求转发的过程，像Nginx一样，服务机器的具体实例，它不会直接去访问IP，它会去Eureka注册中心拿到服务的实例ID，即服务的名字。我再次使用客户端的负载均衡ribbon访问其中服务实例中的一台。

　　API网关主要为了服务本身对外的调用该怎么调用来解决的，还有解决权限校验的问题，你可以在这里整合调用一系列过滤器的，例如整合shiro,springsecurity之类的东西。

Spring Cloud Zuul作用示意图

![](img/wangguan.png)

Zuul可以通过加载动态过滤机制，从而实现以下各项功能：

　　1.验证与安全保障: 识别面向各类资源的验证要求并拒绝那些与要求不符的请求。

　　2.审查与监控: 在边缘位置追踪有意义数据及统计结果，从而为我们带来准确的生产状态结论。

　　3.动态路由: 以动态方式根据需要将请求路由至不同后端集群处。

　　4.压力测试: 逐渐增加指向集群的负载流量，从而计算性能水平。

　　5.负载分配: 为每一种负载类型分配对应容量，并弃用超出限定值的请求。

　　6.静态响应处理: 在边缘位置直接建立部分响应，从而避免其流入内部集群。

　　7.多区域弹性: 跨越AWS区域进行请求路由，旨在实现ELB使用多样化并保证边缘位置与使用者尽可能接近。

### Zuul实例

前期准备(利用前面的实例)：

1、一个服务注册中心springcloud-eureka，端口7070

2、一个生产者服务springcloud-feign-client-producer向服务注册中心注册端口7071

3、两个消费者服务springcloud-feign-client-consumer，分别向服务注册中心注册，端口7171，7272

#### 创建zuul服务步骤

##### 新建springcloud项目，配置zuul依赖

依赖：

```xml
<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-starter-parent</artifactId>
				<version>Edgware.SR3</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
</dependencyManagement>
<dependencies>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-zuul</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-eureka</artifactId>
	</dependency>
</dependencies>
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

入口类：

```java
@EnableAutoConfiguration
@EnableEurekaClient
@EnableZuulProxy
@SpringBootApplication
public class SpringcloudzuulApplication {
	
	public static void main(String[] args) {
		SpringApplication.run(SpringcloudzuulApplication.class, args);
	}
}
```

application.yml

```yaml
server:
  port: 7777  #端口号
 
spring:
  application:
    name: service-zuul  #服务注册中心测试名
 
zuul:
  routes:
    api-a:
      path: /producer/**
      serviceId: producer  #如果是/producer/**路径下的请求，则跳转到producer
    api-b:
      path: /consumer/**
      serviceId: consumer  #如果是/consumer/**路径下的请求，则跳转到consumer
 
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:7070/eureka/  #服务注册中心
```

首先指定服务注册中心的地址为http://localhost:7070/eureka/，服务的端口为7777，服务名为service-zuul；以/producer/ 开头的请求都转发给producer服务；以/consumer/开头的请求都转发给consumer服务；

![](img/zuulURL.png)

我们可以通过下表中的示例来进一步理解这三个通配符的含义并进行参考使用:

![](img/zuulURL2.png)

#### 自定义过滤器

##### 1、过滤器类

定义自己的过滤类，继承ZuulFilter重新方法如下。在run方法里可以获取上下文，以及做响应处理。

```java
public class AccessFilter extends ZuulFilter {
    private static Logger log = LoggerFactory.getLogger(AccessFilter.class);
    @Override
    public String filterType() {
        return "pre";
    }
    @Override
    public int filterOrder() {
        return 0;
    }
    @Override
    public boolean shouldFilter() {
        return true;
    }
    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        HttpServletRequest request = ctx.getRequest();
        log.info("send {} request to{}", request.getMethod(), request.getRequestURL().toString());
        Object accessToken = request.getParameter("accessToken");
        if (accessToken == null) {
            log.warn("accessToken is empty");
            ctx.setSendZuulResponse(false);
            ctx.setResponseStatusCode(401);
            try {
                ctx.getResponse().getWriter().write("accessToken is empty");
            } catch (Exception e) {
            }
            return null;
        }
        log.info("access is ok");
        return null;
    }
}
```

filterType: 过滤器的类型，它决定过滤器在请求的哪个生命周期中执行。这里定义为pre,代表会在请求被路由之前执行。

- `pre`：可以在请求被路由之前调用
- `route`：在路由请求时候被调用
- `post`：在route和error过滤器之后被调用
- `error`：处理请求时发生错误时被调用

filterOrder: 过滤器的执行顺序。当请求在一个阶段中存在多个过滤器时，需要根据该方法返回的值来依次执行。数值越小越先执行。
shouldFilter: 判断该过滤器是否需要被执行。这里我们直接返回了true,因此该过滤器对所有请求都会生效。实际运用中我们可以利用该函数来指定过滤器的有效范围。

run: 过滤器的具体逻辑。这里我们通过ctx.setSendZuulResponse(false)令zuul过滤该请求，不对其进行路由，然后通过ctx.setResponseStatusCode(401)设置了其返回的错误码，当然也可以进 一步优化我们的返回，比如，通过ctx.setResponseBody(body)对返回的body内容进行编辑等。

##### 2、启动加载配置

```java
@Bean
public AccessFilter accessFilter() {
	return new AccessFilter();
}
```

##### 测试

1、访问http://localhost:7777/

2、访问http://localhost:7072/consumer/hello

3、访问http://localhost:7072/consumer/hello?accessToken=consumer

#### Zuul负载均衡

省略N字

