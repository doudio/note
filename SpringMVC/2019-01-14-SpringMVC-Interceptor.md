> #### SpringMVC interceptor

1. 定义类实现`HandlerInterceptor`接口
2. 在`SpringMVC`核心配置文件中进行配置
3. 编写页面发起请求

> 定义类实现`HandlerInterceptor`接口

```java
public interface HandlerInterceptor {

	/**
	 * 该方法在目标方法之前被调用.
	 * 若返回值为 true, 则继续调用后续的拦截器和目标方法. 
	 * 若返回值为 false, 则不会再调用后续的拦截器和目标方法. 
	 * 
	 * 可以考虑做权限. 日志, 事务等. 
	 */
	boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;

	/**
	 * 调用目标方法之后, 但渲染视图之前. 
	 * 可以对请求域中的属性或视图做出修改. 
	 */
	void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception;

	/**
	 * 渲染视图之后被调用. 释放资源
	 */
	void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception;

}
```

> 在`SpringMVC`核心配置文件中进行配置

```xml
<mvc:interceptors>
    <bean class="com.znsd.springmvc.interceptor.FirstInterceptor"></bean>

    <mvc:interceptor>
		<mvc:mapping path="/path"/>
        <bean class="com.znsd.springmvc.interceptor.SecondInterceptor"></bean>
	</mvc:interceptor>
</mvc:interceptors>
```

> 注意: 

1. `URL`中包含该路径: `<mvc:mapping path="/path"/>`
2. `URL`中不包含该路径: `<mvc:exclude-mapping path="/path"/>`
3. 拦截器的执行顺序: 是由配置从上至下进行加载的, 而执行规则符合`责任链设计模式`

