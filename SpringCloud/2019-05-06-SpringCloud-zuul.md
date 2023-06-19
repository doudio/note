> ## SpringCloud zuul

### 简介

　　Zuul 是Netflix 提供的一个开源组件，也是核心组件，致力于在云平台上提供动态路由，监控，弹性，安全等边缘网关服务的框架。其功能很多，比如反向代理，负载均衡还有权限控制等功能。

#### 为什么需要网关呢？

　　我们知道我们要进入一个服务本身，很明显我们没有特别好的办法，直接输入IP地址+端口号，我们知道这样的做法很糟糕的，这样的做法大有问题，首先暴露了我们实体机器的IP地址，别人一看你的IP地址就知道服务部署在哪里，让别人很方便的进行攻击操作。

　　第二，我们这么多服务，我们是不是要挨个调用它呀，我们这里假设做了个权限认证，我们每一个客户访问的都是跑在不同机器上的不同的JVM上的服务程序，我们每一个服务都需要一个服务认证，这样做烦不烦呀，明显是很烦的。那么我们这时候面临着这两个极其重要的问题，这时我们就需要一个办法解决它们。首先，我们看IP地址的暴露和IP地址写死后带来的单点问题，我是不是对这么服务本身我也要动态的维护它服务的列表呀，我需要调用这服务本身，是不是也要一个负载均衡一样的玩意，还有关于IP地址暴露的玩意，我是不是需要做一个代理呀，像Nginx的反向代理一样的东西，还有这玩意上部署公共的模块，比如所有入口的权限校验的东西。因此我们现在需要Zuul API网关。它就解决了上面的问题，你想调用某个服务，它会给你映射，把你服务的IP地址映射成某个路径，你输入该路径，它匹配到了，它就去替你访问这个服务，它会有个请求转发的过程，像Nginx一样，服务机器的具体实例，它不会直接去访问IP，它会去Eureka注册中心拿到服务的实例ID，即服务的名字。我再次使用客户端的负载均衡ribbon访问其中服务实例中的一台。

　　API网关主要为了服务本身对外的调用该怎么调用来解决的，还有解决权限校验的问题，你可以在这里整合调用一系列过滤器的，例如整合shiro,springsecurity之类的东西。

Spring Cloud Zuul作用示意图

![](znsd-springCloud/wangguan.png)

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
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zuul</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

入口类：

```java
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

![](znsd-springCloud/zuulURL.png)

我们可以通过下表中的示例来进一步理解这三个通配符的含义并进行参考使用:

![](znsd-springCloud/zuulURL2.png)

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