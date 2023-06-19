> #### SpringMVC Hello World

1. 导入jar包
2. 配置 DispatcherServlet
3. 编写类 处理请求
4. 编写 SpringMVC 配置文件
5. 编写页面发送请求

> 导入jar包

1. commons-logging-X.x.x.jar
2. spring-aop-X.x.x.RELEASE.jar
3. spring-beans-X.x.x.RELEASE.jar
4. spring-context-X.x.x.RELEASE.jar
5. spring-core-X.x.x.RELEASE.jar
6. spring-expression-X.x.x.RELEASE.jar
7. spring-web-X.x.x.RELEASE.jar
8. spring-webmvc-X.x.x.RELEASE.jar

> maven 配置

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.0.0.RELEASE</version>
</dependency>
```

> 配置 SpringMVC 过滤器

```xml
<!-- 配置 DispatcherServlet -->
<servlet>
    <servlet-name>springDispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 配置 SpringMVC 配置文件的路径和名称 -->
	<init-param>
    	<param-name>contextConfigLocation</param-name>
		<param-value>classpath:springmvc.xml</param-value>
	</init-param>
    <!-- 当前web程序开启时创建 -->
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>springDispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

1. SpringMVC 默认的配置文件路径: /WEB-INF/`<servlet-name>`-servlet.xml

> 编写类处理请求

```java
@Controller
public class HelloWorld {

	@RequestMapping("/helloworld")
	public String helloWorld () {
		System.out.println("com.znsd.springmvc.handlers.HelloWorld.helloWorld()");
		return "success";
	}
    
}
```

> 编写 SpringMVC 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd">

	<!-- 开启扫描的包 -->
	<context:component-scan base-package="com.znsd.springmvc.handlers" />

	<!-- 配置视图解析器 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/views/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>

</beans>
```

1. 返回值会通过视图解析器解析为实际物理视图
2. 对于 InternalResourceViewResolver 视图解析器, 会做如下的解析:
   1. `prefix` + `return` + `suffix` > 然后做转发操作

> 编写页面发送请求

```html
<a href="helloworld">SpringMVC Hello World</a>
```

