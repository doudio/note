> ### SpringAutowire

```xml
<!-- autowire="constructor" : 通过构造器注入, 构造器根据类型进行判断 -->
<!-- autowire="byType" : 通过set方法注入, 根据类型进行判断 -->
<!-- autowire="byName" : 通过set方法注入, bean : id 属性进行注入 -->
<bean id="studentService" class="com.znsd.spring.bean.StudentService"></bean>
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       default-autowire="byName">
    <!-- 配置全局自动注入 : default-autowire="byName" -->
```

> ### 实现 `Aware` 接口注入一些常用对象 AND 属性

```java
public class StudentService implements BeanNameAware, ApplicationContextAware {

	private String beanName;

	/**
	 * 注入 spring 核心对象: ClassPathXmlApplicationContext
	 */
	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		System.out.println(applicationContext.getClass());

		StudentService bean = (StudentService)applicationContext.getBean(beanName);
		bean.dao();
	}

	/**
	 * 注入在 spring 配置中的 bean : id
	 */
	@Override
	public void setBeanName(String beanName) {
		this.beanName = beanName;
	}
}
```
