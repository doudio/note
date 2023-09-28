> ### SpringCloud login `Eureka`身份认证

> 在`服务注册中心`添加`dependency`依赖 `::` 安全服务注册

```xml
<!-- 安全注册服务 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

> 添加安全服务配置

```properties
#开启安全机制设置用户名密码
security.basic.enabled=true
security.user.name=root
security.user.password=root

#服务注册地址例子如下
eureka.client.serviceUrl.defaultZone=http://admin:123456@localhost:7070/eureka/
```

> 在`服务`中的配置连接服务注册中心的`eureka.client.serviceUrl.defaultZone`中添加的值
>
> http://`用户名`:`密码`@localhost:7070/eureka/

---

> #### 服务(`server`) 健康检查

> 查看服务信息 `::` http://localhost:8080/info
>
> 查看健康状态 `::` http://localhost:8080/health

> >  eureka提供出了两个接口：
>
> `HealthIndicator` `::` （健康指示器）用来感知服务的状态。
>
> `HealthCheckHandler` `::`（健康检查处理器）用来处理健康结果。

> 在`服务`端 `pom` 文件中添加`dependency`配置

```xml
<!--健康检查依赖-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

> 在当前`服务`中编写健康逻辑

```java
@Component
public class MyHealthIndicator implements HealthIndicator{

    public Health health() {
        if( true || false ) {
            return new Health.Builder(Status.UP).build();
        } else {
            return new Health.Builder(Status.DOWN).build();
        }
    }

}
```

> `Status.UNKNOWN` : 未知状态
>
> `Status.UP` : 正常状态
>
> `Status.DOWN` : 故障
>
> `Status.OUT_OF_SERVICE` : 退出	

> 加快`服务`心跳发送

```properties
#更新实例信息的变化到Eureka服务端的间隔时间，单位为秒,默认为30秒
eureka.client.instanceInfoReplicationIntervalSeconds=10
```

> 将健康状态发送到`服务注册`器

```java
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
```

