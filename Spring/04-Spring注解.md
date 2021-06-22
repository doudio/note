> ### Spring注解

1. 编写类添加注解
2. 在 spring 配置文件中, 声明注解扫描的包路径
3. 编写 JUnit Test Case 测试

> 编写类添加注解

```java
package com.znsd.spring.dao.impl;

import org.springframework.stereotype.Repository;

import com.znsd.spring.dao.UserDao;

@Repository
public class UserDaoImpl implements UserDao {

	public String isExists(String userName) {
		System.out.println("com.znsd.spring.dao.impl.UserDaoImpl.isExists(String)");
		return userName;
	}

}
```

```java
package com.znsd.spring.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.znsd.spring.dao.UserDao;
import com.znsd.spring.service.UserService;

@Service
public class UserServiceImpl implements UserService {

	@Autowired
	private UserDao userDao;

	public String isExists(String userName) {
		System.out.println("UserServiceImpl.isExists(String)");
		return userDao.isExists(userName);
	}

}
```

> 在 spring 配置文件中, 声明注解扫描的包路径

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
						http://www.springframework.org/schema/beans/spring-beans.xsd
				        http://www.springframework.org/schema/context
				        http://www.springframework.org/schema/context/spring-context.xsd">

	<context:component-scan base-package="com.znsd.spring.dao" />
	<context:component-scan base-package="com.znsd.spring.service" />

</beans>
```

> 注意导入: context

---

> 编写 JUnit Test Case 测试

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.3.18.RELEASE</version>
    <scope>test</scope><!-- 在 maven 项目, 编译后不会留在项目中 -->
</dependency>
```

```java
package com.znsd.spring.service;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring.xml")
public class UserServiceTest {

	@Autowired
	private UserService userService;
	
	@Test
	public void testIsExists() {
		System.out.println("test:" + userService.isExists("administrator"));
	}

}
```

---

| @:注解                                             | 注解说明                                                     |
| -------------------------------------------------- | ------------------------------------------------------------ |
| **@Controller**                                    | 声明Action组件                                               |
| **@Service**                                       | 声明Service组件    @Service("myMovieLister")                 |
| **@Repository**                                    | 声明Dao组件                                                  |
| **@Component**                                     | 泛指组件, 当不好归类时.                                      |
| @Resource                                          | 用于注入，( j2ee提供的 )默认按名称装配，@Resource(name="beanName") |
| **@Autowired**                                     | 用于注入，(Spring提供的) 默认按类型装配                      |
| **@Transactional**( rollbackFor={Exception.class}) | 事务管理                                                     |
| **@Scope**("prototype")                            | 设定bean的作用域                                             |
| @Qualifier                                         | 通过指定确切的将被连线的 bean，@Autowired 和 @Qualifier 注解可以用来删除混乱。 |
| @Required                                          | @Required 注解应用于 bean 属性的 setter 方法。               |

