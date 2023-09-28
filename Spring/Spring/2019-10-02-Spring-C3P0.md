> ### Spring-C3P0

1. 导入jar
2. 在 spring 配置文件中添加: context
3. 配置数据源 bean 
4. 通过 ApplicationContext 获取数据源

> 导入 jar

```xml
c3p0-0.9.1.2.jar
commons-logging-1.1.1.jar
mysql-connector-java-5.1.7-bin.jar
spring-aop-4.0.0.RELEASE.jar
spring-beans-4.0.0.RELEASE.jar
spring-context-4.0.0.RELEASE.jar
spring-core-4.0.0.RELEASE.jar
spring-expression-4.0.0.RELEASE.jar
```

> 在 spring 配置文件中添加: context 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
						http://www.springframework.org/schema/beans/spring-beans.xsd
						http://www.springframework.org/schema/context 
						http://www.springframework.org/schema/context/spring-context-4.0.xsd">
```

> 配置数据源 bean 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
						http://www.springframework.org/schema/beans/spring-beans.xsd
						http://www.springframework.org/schema/context 
						http://www.springframework.org/schema/context/spring-context-4.0.xsd">

	<context:property-placeholder location="classpath:db.properties" />

	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 使用外部化属性文件中的属性 -->
		<property name="user" value="${user}"></property>
		<property name="password" value="${password}"></property>
		<property name="driverClass" value="${driverClass}"></property>
		<property name="jdbcUrl" value="${jdbcUrl}"></property>
	</bean>

</beans>
```

> db.properties

```properties
user=root
password=root
driverClass=com.mysql.jdbc.Driver
jdbcUrl=jdbc:mysql::///test
```

> 通过 ApplicationContext 获取数据源

```java
public static void main(String[] args) throws SQLException {

	ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");

	DataSource dataSource = (DataSource) context.getBean("dataSource");
	
	System.out.println(dataSource.getConnection());

}
```

