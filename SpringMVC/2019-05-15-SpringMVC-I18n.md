> #### SpringMVC-I18n

> 关于国际化

1. 在页面上能够根据浏览器语言的设置国际化资源文件进行解析
2. 可以在目标方法中获取国际化资源文件 Locale 对应的消息
3. 可以通过超链接切换 Locale,而不再依赖与浏览器语言设置情况

> 解决方案

1. 使用 `JSTL` 的 `fmt` 标签
2. 在 `bean` 中注入 `ResourceBundleMessageSource` 的示例, 使用其对应的 `getMessageSource` 方法
3. 配置`LocalResolver`和`LocaleChangeInterceptor`

---

> #### 使用 `JSTL` 的 `fmt` 标签

1. 配置`messageSource`
2. 在`src`下编写国际化资源文件`.properties`
3. 在页面中导入`ftm`标签使用`fmt`标签获取值

> 配置`messageSource`

```xml
<!-- 配置国际化资源文件 -->
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
    <property name="basename" value="i18n"></property>
</bean>
```

> 在`src`下编写国际化资源文件`.properties`

* i18n_en_US.properties

```properties
i18n.username=UserName
i18n.password=Password
```

* i18n_zh_CN.properties

```properties
i18n.username=\u7528\u6237\u540D
i18n.password=\u5BC6\u7801
```

> 在页面中导入`ftm`标签使用`fmt`标签获取值

```jsp
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>

<fmt:message key="i18n.username"></fmt:message>
<br />
<fmt:message key="i18n.password"></fmt:message>
```

> #### 在 `bean` 中注入 `ResourceBundleMessageSource` 的示例

```java
@Autowired
private ResourceBundleMessageSource messageSource;

@RequestMapping("i18n")
public String i18n (Locale locale) {
    String username = messageSource.getMessage("i18n.username", null, locale);
    System.out.println(username);
    return "i18n";
}
```

> #### 可以通过超链接切换 Locale,而不再依赖与浏览器语言设置情况

1. 在 SpringMVC 核心配置文件中配置
2. 编写页面通过`ftm`输出国际化资源文件中的值
3. 编写`a`标签进行访问, 访问时带有`locale=zh_CN`参数

> 在 SpringMVC 核心配置文件中配置

```xml
<bean id="localeResolver" class="org.springframework.web.servlet.i18n.SessionLocaleResolver"></bean>

<mvc:interceptors>
    <bean class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor"></bean>
</mvc:interceptors>
```

> 编写页面通过`ftm`输出国际化资源文件中的值

```jsp
<fmt:message key="i18n.username"></fmt:message>
<br />
<fmt:message key="i18n.password"></fmt:message>
```

> 编写`a`标签进行访问

```html
<a href="i18n?locale=zh_CN">中文</a>
<a href="i18n?locale=en_US">英文</a>
```

