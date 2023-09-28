> ### SpringCloud Proxy

> 新建一个 springcloud 项目，配置zuul依赖

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
```

> 在 application.yml 文件中添加配置

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

> 注意: 
>
> routes:
> ​    api-`随机的`:
> ​      path: /`请求时的URL`/**
> ​      serviceId: `转发到的那个服务ID`
>
> >  `path`: 的通配符遵循统一格式

> 编写服务调用`间`的过滤器

> 定义类实现`ZuulFilter`接口

```java
@Component
public class AccessFilter extends ZuulFilter {
	
	/**
	 * 过滤器的生命周期 
	 */
	@Override
	public String filterType() {
		return "pre";
	}

	/**
	 * 过滤器的执行顺序, 最好不要定义成负数, SpringCloud 自定义的过滤器是已负数形式的
	 */
	@Override
	public int filterOrder() {
		return 0;
	}

	/**
	 * 是否执行run方法
	 */
	public boolean shouldFilter() {
		System.out.println("AccessFilter.shouldFilter() false");
		return false;
	}

	/**
	 * 编写过滤器业务逻辑
	 */
	public Object run() {
		RequestContext ctx = RequestContext.getCurrentContext();
		HttpServletRequest request = ctx.getRequest();
		Object accessToken = request.getParameter("accessToken");
		if (accessToken == null) {
			ctx.setSendZuulResponse(false);
			ctx.setResponseStatusCode(401);
			try {
				ctx.getResponse().getWriter().write("accessToken is empty");
			} catch (Exception e) {
			}
			return null;
		}
		return null;
	}

}
```

> 过滤器生命周期的值

| 值    | 描述                               |
| ----- | ---------------------------------- |
| pre   | 可以在请求被路由之前调用           |
| route | 在路由请求时候被调用               |
| post  | 在`route`和`error`过滤器之后被调用 |
| error | 处理请求时发生错误时被调用         |

