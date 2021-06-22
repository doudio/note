> ### Spring-JDBC

1. 导入数据库连接池 与 数据库连接 jar
2. 在 Spring 配置文件中配置基本信息
3. 编写类 继承 JdbcDaoSupport
4. 若使用 注解方式 通过构造器注入 dataSource, 配置文件通过依赖注入实现, 注入dataSource

> maven 配置

```xml
<dependencies>
    <!-- spring -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.3.18.RELEASE</version>
    </dependency>

    <!-- spring-jdbc -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>4.3.18.RELEASE</version>
    </dependency>

    <!-- spring-test -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>4.3.18.RELEASE</version>
        <scope>test</scope>
    </dependency>

    <!-- beans:注解 -->
    <dependency>
        <groupId>aopalliance</groupId>
        <artifactId>aopalliance</artifactId>
        <version>1.0</version>
    </dependency>

    <!-- aop:切面 -->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.6.12</version>
    </dependency>

    <!-- 阿里巴巴-连接池 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.5</version>
    </dependency>

    <!-- 数据库连接池 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.38</version>
    </dependency>

</dependencies>
```

> spring 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context 
		http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop 
		http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

	<!-- 导入外部资源文件 -->
	<context:property-placeholder location="database.properties" />
	
	<!-- 声明包扫描的路径 -->
	<context:component-scan base-package="com.znsd.spring.dao" />
	<context:component-scan base-package="com.znsd.spring.service" />

	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="username" value="${jdbc.username}" ></property>
		<property name="password" value="${jdbc.password}" ></property>
		<property name="url" value="${jdbc.url}" ></property>
        <!-- DruidDataSource: 会根据URL判断选择那个数据库驱动 -->
	</bean>

</beans>
```

> 编写类 继承 JdbcDaoSupport

```java
package com.znsd.spring.dao.impl;

import javax.sql.DataSource;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.support.JdbcDaoSupport;
import org.springframework.stereotype.Repository;

import com.znsd.spring.dao.UserDao;

@Repository
public class UserDaoImpl extends JdbcDaoSupport implements UserDao {
	
	@Autowired
	public UserDaoImpl(DataSource dataSource){
		this.setDataSource(dataSource);
	}
	
	public boolean add(Integer in, Integer number) {
		String sql = "UPDATE `account` SET `pay` = `pay` + ? WHERE `id` = ?";
		return this.getJdbcTemplate().update(sql, number, in) == 1;
	}

	public boolean minus(Integer out, Integer number) {
		String sql = "UPDATE `account` SET `pay` = `pay` - ? WHERE `id` = ?";
		return this.getJdbcTemplate().update(sql, number, out) == 1;
	}

}
```

* 若不使用注解形式 则通过配置注入 dataSource

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="username" value="${jdbc.username}" ></property>
    <property name="password" value="${jdbc.password}" ></property>
    <property name="url" value="${jdbc.url}" ></property>
</bean>

<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"></property>
</bean>

<bean id="userDaoImpl" class="com.znsd.spring.dao.impl.UserDaoImpl">
    <property name="jdbcTemplate" ref="jdbcTemplate"></property>
</bean>
```

