> ### 01-Spring Bean的 init AND destroy

```java
public static void main(String[] args) {
    
	ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("classpath:beans.xml");
	
    HelloWorld helloWorld = (HelloWorld) context.getBean("hello");
    HelloWorld helloWorld2 = (HelloWorld) context.getBean("hello");

    System.out.println("helloWorld == helloWorld2 : " + (helloWorld == helloWorld2));

    context.close();// 模拟一次 request 结束
    
}
```

> ##### 作用域范围

```xml
<!-- scope="singleton"	: 单利模式(默认值) -->
<!-- scope="prototype" AND scope="request"	: 每request都创建新实例 -->
<!-- scope="session" : 每次session创建新实例 -->
<!-- scope="application" : 在整个应用程序中有效, 同web -->
<bean id="hello" class="com.znsd.spring.bean.HelloWorld" scope="prototype"></bean>
```

> ==**注意**==: 若 bean : scope="prototype" 则 bean的销毁不被 IOC 容器管理了

> ### 通过配置声明: init AND destroy

```xml
<bean id="hello" class="com.znsd.spring.bean.HelloWorld" scope="singleton" init-method="initMethod" destroy-method="destroyMethod"></bean>
```

> ### 配置全局: init AND destroy

```xml
<!-- 配置全局初始化销毁方法 -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd"
       default-init-method="initMethod"
       default-destroy-method="destroyMethod"> 
```

> #### 通过实现接口方式来调用: init AND destroy

```java
/**
 * implements InitializingBean, DisposableBean
 * 	InitializingBean	: 初始化接口
 *	DisposableBean		: 销毁接口
 */
public class HelloWorld implements InitializingBean, DisposableBean {

	@Override
	public void destroy() throws Exception {
		System.out.println("implements DisposableBean destroy");
	}

	@Override
	public void afterPropertiesSet() throws Exception {
		System.out.println("implements InitializingBean afterPropertiesSet");
	}

}
```

> #### 为一个bean取别名 AND 获取Bean的方式 AND 导入外部 Spring 配置文件

```xml
<!-- 为一个bean取别名 -->
<alias name="hello" alias="AShello"/>

<!-- 获取Bean的方式:
   通过: context.getBean("id");			: id是唯一的, 只能配置一个
   通过: context.getBean("name");			: name 可以配置多个, 多个name用: [ , ] 分割
   通过: context.getBean(Objec.class);	: 通过对象对应的 Class来获取, 若配置中有两个相同的 Class 则 Spring 无法锁定对象
-->
<bean id="hello" name="name1,name2" class="com.znsd.spring.bean.HelloWorld" scope="singleton" init-method="initMethod" destroy-method="destroyMethod"></bean>

<!-- 导入外部 Spring 配置文件 -->
<import resource="classpath:userService.xml"/>
```

> ##### 若需要加载多个配置文件可以通过

```java
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml", "beans2.xml", ...);
```

> ##### 想避免强制转换

```java
Class classes = context.getBean("ID", IdClass.class);
```

> 配置bean的懒加载

```xml
<bean id="classId" class="package.ClassName" lazy-init="true"></bean>
```

>  默认为: lazy-init="false"
>
> 一般情况下 bean 的初始化在 ApplicationContext 初始化时创建, 配置懒加载后用到对象后才加载
