> ### struts2拦截器

1. 定义类, 实现 `Interceptor` 或 继承 `AbstractInterceptor` 推荐使用继承
2. 在 Struts2 配置文件中 , 注册拦截器
3. 在 Action 中配置使用

---



1. `Interceptor` 接口中提供: 
      1. void init();
      2. void destroy();
      3. String intercept(ActionInvocation invocation) throws Exception;
   2. `AbstractInterceptor`  类中对 不常用的 [ void init(); , void destroy(); ] 做了一个空实现
      1. public void init() {}
      2. public void destroy() {}
      3. public abstract String intercept(ActionInvocation invocation) throws Exception;
3. 在 intercept(); 方法中编写业务逻辑

```java
/**
 * 自定义拦截器
 */
public class TimeInterceptor extends AbstractInterceptor {
    
	@Override
	public String intercept(ActionInvocation invocation) throws Exception {
		
		long startTime = System.currentTimeMillis();
		
		String result = invocation.invoke();
		
		long endTime = System.currentTimeMillis();
		
		System.out.println("result: " + result + ", 运行了多少( " + (endTime - startTime) + " )毫秒!");
		
		return result;
	}

}
```

*注意*

1. String result = invocation.invoke(); 将执行权 交给下一个拦截器, 或者方法

---

> 在 Struts2 配置文件中 , 注册拦截器

```xml
<interceptors>
    <!-- 注册拦截器 -->
    <interceptor name="timeInterceptor" class="com.znsd.struts2.action.TimeInterceptor" />
    <!-- 注册拦截器栈 -->
    <interceptor-stack name="stackName" />
</interceptors>
```

> 在 action 中配置使用

```xml
<action name="conversion" class="com.znsd.struts2.action.ConversionAction">
    <interceptor-ref name="defaultStack"></interceptor-ref>
    <interceptor-ref name="timeInterceptor"></interceptor-ref>

    <result>/success.jsp</result>
    <result name="input">/index.jsp</result>
</action>
```

*注意*

1. 在使用自定义拦截器时, `默认的拦截器栈会失效`
2. 在自定默认拦截器时`建议放在第一个`