> ### Maven项目整合SSH

1. 编写数据库基本连接信息
2. 配置 Spring 核心文件
3. 配置 web.xml 文件
4. 添加 struts.xml 



> #### 编写数据库基本连接信息: `database.properties`

```properties
jdbc.username=root
jdbc.password=root
jdbc.url=jdbc:mysql://localhost:3306/database_db
jdbc.driver=com.mysql.jdbc.Driver
```

> #### 配置 Spring 核心文件

1. 配置数据库连接池
2. 配置 Hibernate 基本信息
3. 配置 Hibernate 事务管理

> 配置数据库连接池

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context 
		http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/aop 
		http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/tx 
		http://www.springframework.org/schema/tx/spring-tx.xsd">
    
<!-- 数据库配置文件位置 -->
<context:property-placeholder location="classpath:database.properties" />

<!-- 数据库连接池 -->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="username" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
    <property name="url" value="${jdbc.url}"></property>
</bean>
```

```xml
<!-- maven:阿里巴巴数据库 - 连接池 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>

<!-- 数据库连接 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.38</version>
</dependency>
```

> 配置 Hibernate 基本信息

```xml
<!-- 配置SessionFactory -->
<bean id="sessionFactory" class="org.springframework.orm.hibernate4.LocalSessionFactoryBean">
	<!-- 将数据源引入: sessionFactory -->
	<property name="dataSource">
		<ref bean="dataSource" />
	</property>

    <property name="hibernateProperties">
		<props>
			<!-- 配置Hibernate : 数据库方言 -->
			<prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
			<!-- 配置Hibernate : 是否显示SQL -->
			<prop key="hibernate.show_sql">true</prop>
			<!-- 配置Hibernate : 格式化SQL -->
			<prop key="hibernate.format_sql">true</prop>
            <prop key="hibernate.hbm2ddl.auto">update</prop>
		</props>
	</property>
        
	<property name="mappingLocations">
		<list>
			<!-- 指定Hibernate : 对象映射文件路径 -->
			<value>classpath:com/znsd/ssh/bean/*.hbm.xml</value>
		</list>
	</property>
</bean>
```

```xml
<!-- hibernate -->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>4.3.11.Final</version>
</dependency>

<!-- spring -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.3.18.RELEASE</version>
</dependency>

<!-- spring-orm -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>4.3.18.RELEASE</version>
</dependency>
```

> 配置 Hibernate 事务管理

```xml
<!-- 配置事务 - 管理器 -->
<bean id="txManager" class="org.springframework.orm.hibernate4.HibernateTransactionManager">
    <property name="sessionFactory" ref="sessionFactory" />
</bean>

<!-- 配置需要开启事务的方法 -->
<tx:advice id="txAdvice" transaction-manager="txManager">
    <tx:attributes>
        <!-- 设置匹配方法 -->
        <tx:method name="find*" propagation="REQUIRED" read-only="true" />

        <tx:method name="insert*" propagation="REQUIRED" />

        <tx:method name="del*" propagation="REQUIRED" />

        <tx:method name="update*" propagation="REQUIRED" />

        <tx:method name="*" propagation="REQUIRED" read-only="true" />
    </tx:attributes>
</tx:advice>

<!-- AOP切面拦截事务 -->
<aop:config>
    <aop:pointcut id="serviceMethod" 
                  expression="execution(* com.znsd.ssh.service.*.*(..))" />
    <aop:advisor advice-ref="txAdvice" pointcut-ref="serviceMethod" />
</aop:config>
```

```xml
<!-- AspectJ : 配置事务管理 -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.1</version>
</dependency>
<dependency>
    <groupId>aopalliance</groupId>
    <artifactId>aopalliance</artifactId>
    <version>1.0</version>
</dependency>

<!-- spring-事务 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>4.3.18.RELEASE</version>
</dependency>
```

> 配置 web.xml 文件

1. 添加 OpenSessionInView 过滤器
2. 添加编码格式过滤器: `可选`
3. 配置Spring核心文件路径
4. Struts2 核心 过滤器
5. 防止spring内存溢出监听器

```xml
<!-- 添加 OpenSessionInView 过滤器, 将 Session 生命周期搬到视图层 -->
<filter>
    <filter-name>OpenSessionInViewFilter</filter-name>
    <filter-class>org.springframework.orm.hibernate4.support.OpenSessionInViewFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>OpenSessionInViewFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

```xml
<!-- 添加编码格式过滤器 -->
<filter>
    <description>字符集过滤器</description>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <description>字符集编码</description>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

```xml
<!-- 配置Spring核心文件路径 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring-*.xml</param-value>
</context-param>
<!-- 用于初始 Spring 配置信息 -->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

```xml
<!-- Struts2 核心 过滤器 -->
<filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>struts2</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

```xml
<!-- 防止spring内存溢出监听器 -->
<listener>
    <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
</listener>
```

> #### 添加 struts.xml 

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>

	<constant name="struts.enable.DynamicMethodInvocation" value="false" />
	<constant name="struts.devMode" value="true" />

	<package name="default" namespace="/" extends="struts-default"></package>
    
</struts>
```

---

> 配置 maven 项目的jdk版本

```xml
<!-- 配置 maven 项目的jdk版本 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

> maven:struts

```xml
<!-- struts -->
<dependency>
    <groupId>org.apache.struts</groupId>
    <artifactId>struts2-core</artifactId>
    <version>2.5.16</version>
</dependency>

<!-- struts2 集成 spring -->
<dependency>
    <groupId>org.apache.struts</groupId>
    <artifactId>struts2-spring-plugin</artifactId>
    <version>2.5.16</version>
</dependency>
```

---

> #### 整合 Struts2 非注解方式使用时应该注意

1. Spring 配置文件中配置[ action, service, dao ]
   1. 在配置 Action 的时候需要注意我们一般需要 `scope="prototype"`
2. Struts2 配置文件下的 action > class 写 Spring 下的 bean > id

> Spring 配置文件中配置[ action, service, dao ]

```xml
<bean id="springAction" class="package.className" scope="prototype">
    <property name="springService" ref="springService"></property>
</bean>
 . . . 
<bean id="SpringDao" class="com.znsd.ssh.dao.impl.BookDaoImpl">
    <property name="sessionFactory" ref="sessionFactory" />
</bean>
```

> Struts2 配置文件下的 action > class 写 Spring 下的 bean > id

```xml
<action name="updateBook" class="springAction" method="method">
    <result>/index.jsp</result>
</action>
```

> #### 整合使用 Struts2 注解方式

1. struts2 配置文件可以放弃
2. 导入 struts2-convention-plugin.jar
3. 使用时常用注解介绍

> 导入 struts2-convention-plugin.jar

```xml
<!-- struts2 注解 -->
<dependency>
    <groupId>org.apache.struts</groupId>
    <artifactId>struts2-convention-plugin</artifactId>
    <version>2.5.16</version>
</dependency>
```

> 使用时常用注解介绍

| 注解                           | 介绍                                      |
| ------------------------------ | ----------------------------------------- |
| @ParentPackage("json-default") | 继承父包[`struts-default`:`json-default`] |
| @Namespace("/")                | 定义命名空间                              |
| @Action                        | 定义一个请求                              |
| @Result                        | 定义一个返回结果                          |
| @Scope("prototype")            | 设置 Action 为多利模式                    |

```java
@ParentPackage("json-default")
@Namespace("/")
public class ProductAction extends ActionSupport {
```

```
@Action(value = "url", results = {
    @Result(name = SUCCESS, location = "/index.jsp")
})
```

```java
@Action(value = "url", results = {
	@Result(name = SUCCESS, type= "json" , params = {"root", "result"})
})
```

