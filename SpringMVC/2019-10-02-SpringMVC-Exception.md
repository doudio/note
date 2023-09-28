> #### SpringMVC Exception

> 异常处理通过`@ExceptionHandler`注解实现
>
> 定义全局异常类: ` @ControllerAdvice`

```java
@ControllerAdvice
public class Abnormal {

	@ExceptionHandler({Exception.class})
	public ModelAndView exception(Exception ex) {
		ModelAndView mv = new ModelAndView("error");
		mv.addObject("ex", ex);
		return mv;
	}
	
}
```

* 异常的捕获顺序为: 从精确匹配到 `Exception`

> `@ResponseStatus`放回的状态码: 可用于修饰`Exception`类

```java
public @interface ResponseStatus {

	/**
	 * 返回的状态码
	 */
	HttpStatus value();

	/**
	 * 返回的错误消息
	 */
	String reason() default "";

}
```

> #### 通过配置文件进行异常声明

```xml
<!-- 配置使用 SimpleMappingExceptionResolver 来映射异常 -->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
            <prop key="java.lang.ArrayIndexOutOfBoundsException">error</prop>
        </props>
    </property>
</bean>	
```

