> #### SpringMVC-Exception

> 在配置 SpringMVC servlet 时出了一个问题

```xml
<servlet>
    <servlet-name>springDispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>springDispatcherServlet</servlet-name>
    <url-pattern>/*</url-pattern>
</servlet-mapping>
```

> 错误点:

```html
<!-- 由于 url-pattern : 习惯了 /* 导致.jsp 页面访问不了: 500  -->
<url-pattern>/*</url-pattern>

<!-- 正确写法应该写成: -->
<url-pattern>/</url-pattern>
```

---

> #### 导入静态资源 `jquery` 404 问题解决方案

```xml
<!-- 将静态资源处理权交给 Tomcat -->
<mvc:default-servlet-handler />

<mvc:annotation-driven></mvc:annotation-driven>
```

1. 在有时候我们访问静态资源的时候 SpringMVC 会抛出没有这个映射
2. 这时候我们可以添加: 	`<mvc:default-servlet-handler />`注解来解决这个问题
3. 但是添加注解后又导致访问方法有不好用了这时候添加: `<mvc:annotation-driven></mvc:annotation-driven>` 注解来解决

---

> SpringMVC 跨域问题解决: 通过`@CrossOrigin`

