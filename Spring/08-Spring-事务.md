> ### Spring-事务

1. Spring 事务管理 API
2. Spring 编程式事务管理
3. Spring 声明事务管理

---

> Spring 事务管理层抽象主要包括3个解耦

| 属性                       | 说明                                 |
| -------------------------- | ------------------------------------ |
| PlatformTransactionManager | 平台事务管理器                       |
| TransactionDefinition      | 事务定义信息(隔离、传播、超时、只读) |
| TransactionStatus          | 事务具体运行状态                     |

> 平台转换管理器

| 事务                                                         | 说明                                        |
| ------------------------------------------------------------ | ------------------------------------------- |
| org.springframework.jdbc.datasource.DataSourceTransactionManager | 使用Spring JDBC或iBatis进行持久化数据时使用 |
| org.springframework.orm.hibernate3.HibernateTransactionManager | 使用Hibernate 3.0版本进行持久化数据使用     |

> 事务传播行为

| 值                        | 说明                                         |
| ------------------------- | -------------------------------------------- |
| PROPAGATION_REQUIRED      | 支持当前事务, 如果不存在就新建一个           |
| PROPAGATION_SUPPORTS      | 支持当前事务, 如果不存在就不使用事务         |
| PROPAGATION_MANDATORY     | 支持当前事务, 如果不存在就, 抛出异常         |
| PROPAGATION_REQUIRES_NEW  | 如果有事务存在, 挂起当前事务,创建一个新事务  |
| PROPAGATION_NOT_SUPPORTED | 以非事务方式运行, 如果有事务存在挂起当前事务 |
| PROPAGATION_NEVER         | 以非事务方式运行, 如果有事务存在, 抛出异常   |
| PROPAGATION_NESTED        | 如果当前事务存在, 则嵌套事务执行             |

> 事务隔离级别

| 值              | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| DEFAULT         | 使用后端数据库的隔离级别                                     |
| READ_UNCOMMITED | 允许读取还未提交的或改变了的数据. 可能导致脏, 幻, 不可重复读 |
| READ_COMMITTED  | 允许在并发事务已提交后读取, 可防止脏读, 但幻都和不可重复读仍可发生 |
| REPEATABLE_READ | 对相同字段的多次读取是一致的, 除非数据被事务本身改变. 可防止脏读, 不可重复读, 但幻都仍可能发生 |
| SERIALIZABLE    | 完全遵从 ACID 的事务隔离级别, 确保不发生[脏,幻,不可重复读] 这在所有的隔离级别中是最慢的, 他是经典的通过完全锁定在事务中涉及的数据表来完成的 |

> #### Spring 编程式事务管理

1. 在 Spring 配置文件中进行配置
   1. 配置事务管理器
   2. 配置事务模板
2. 将对象注入到Service 层中

---

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
	<context:component-scan base-package="com.znsd.spring" />
	
    <!-- 配置数据库连接池 -->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="username" value="${jdbc.username}" ></property>
		<property name="password" value="${jdbc.password}" ></property>
		<property name="url" value="${jdbc.url}" ></property>
	</bean>
	
	<!-- 配置事务管理器 -->
	<bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<!-- 事务模板 -->
	<bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
        
		<property name="transactionManager" ref="dataSourceTransactionManager" />		
		<!-- 事务传播行为 -->
		<!-- <property name="propagationBehavior" value="" /> -->

		<!-- 事务隔离级别 -->
		<!-- <property name="isolationLevel" value="" /> -->

		<!-- 是否是只读事务[只读，修改，删除，添加都不能够操作] -->
		<!-- <property name="readOnly" value="true" /> -->

		<!-- 事务退出时间 -->
		<!-- <property name="timeout" value="" /> -->
	</bean>
	
</beans>
```

> 将对象注入

```java
package com.znsd.spring.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.TransactionCallback;
import org.springframework.transaction.support.TransactionCallbackWithoutResult;
import org.springframework.transaction.support.TransactionTemplate;

import com.znsd.spring.dao.UserDao;
import com.znsd.spring.service.UserService;

@Service
public class UserServiceImpl implements UserService {

	@Autowired
	private UserDao userDao;
	
	@Autowired
	private TransactionTemplate template;

	/* 无返回值 */
	public void transfer(Integer in, Integer out, Integer number) {
		template.execute(new TransactionCallbackWithoutResult() {
			@Override
			protected void doInTransactionWithoutResult(TransactionStatus status) {
				System.out.println("transfer(Integer, Integer, Integer)");
				
				userDao.add(in, number);
				
				if (number > 100) {
					throw new RuntimeException("转账金额过大");
				}
				
				userDao.minus(out, number);
			}
		});
	}
	
	/* 带有返回值 */
	public String transferAndReturn(Integer in, Integer out, Integer number) {
		return template.execute(new TransactionCallback<String>() {

			public String doInTransaction(TransactionStatus status) {
				System.out.println("transfer(Integer, Integer, Integer)");
				userDao.add(in, number);
				
				if (number > 100) {
					throw new RuntimeException("转账金额过大");
				}
				
				userDao.minus(out, number);
				
				return "return-values";
			}
			
		});
	}

}
```

---

> #### Spring 声明事务管理

1. 推荐使用, 声明事务管理
2. 在不需要改变其源代码的情况下添加事务, 通过 Spring 配置来实现

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>

	<!-- 导入外部资源文件 -->
	<context:property-placeholder location="database.properties" />

	<!-- 声明包扫描的路径 -->
	<context:component-scan base-package="com.znsd.spring" />

	<!-- 数据库连接池 -->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="username" value="${jdbc.username}"></property>
		<property name="password" value="${jdbc.password}"></property>
		<property name="url" value="${jdbc.url}"></property>
	</bean>

	<!-- 配置 Spring 的 JDBC -->
	<bean id="jdbcTemplate"
		class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<!-- 配置事务管理器 -->
	<bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" value="#{dataSource}" />
	</bean>

	<!-- 配置(定义)事务详细规则 -->
	<tx:advice id="txUserService" transaction-manager="transactionManager">
		<tx:attributes>
			<tx:method name="to" propagation="REQUIRED" />
		</tx:attributes>
	</tx:advice>
	
	<!-- 配置事务切面 -->
	<aop:config>
		<aop:pointcut id="txService"
                      expression="execution(* com.znsd.spring.service.*.*(..))" />
		<aop:advisor advice-ref="txUserService" pointcut-ref="txService" />
	</aop:config>
	
</beans>
```

> Spring 声明事务管理-注解形式

1. 在 Spring 配置文件中声明, 事务使用注解形式
2. 在需要使用事务的方法上添加注解: `@Transactional`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>

	<!-- 导入外部资源文件 -->
	<context:property-placeholder location="database.properties" />

	<!-- 声明包扫描的路径 -->
	<context:component-scan base-package="com.znsd.spring" />

	<!-- 数据库连接池 -->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="username" value="${jdbc.username}"></property>
		<property name="password" value="${jdbc.password}"></property>
		<property name="url" value="${jdbc.url}"></property>
	</bean>

	<!-- 配置 Spring 的 JDBC -->
	<bean id="jdbcTemplate"
		class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<!-- 配置事务管理器 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" value="#{dataSource}" />
	</bean>

	<!-- 配置(定义)事务详细规则 -->
	<tx:annotation-driven />

</beans>
```

```java
package com.znsd.spring.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import com.znsd.spring.dao.UserDao;
import com.znsd.spring.service.UserService;

@Service
public class UserServiceImpl implements UserService {

	@Autowired
	private UserDao userDao;
	
	@Transactional
	public void to(Integer in, Integer out, Integer number) {
		userDao.add(in, number);
		
		if (number > 100) throw new RuntimeException("转账金额过大");
		
		userDao.minus(out, number);
	}

}
```

