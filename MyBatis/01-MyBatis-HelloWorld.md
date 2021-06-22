> #### MyBatis HelloWorld

1. 创建数据库与表
2. 导入`MyBatis`所需`jar`包
3. 编写`MyBatis`核心配置文件
4. 定义封装表数据的类
5. 为类编写配置`Mapper`文件用户存放一些`sql`
6. 在`MyBatis`总配置文件中导入
7. 编写测试类进行数据查询

> 创建数据库与表

```sql
CREATE TABLE `tbl_employee` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `last_name` VARCHAR(255) DEFAULT NULL,
  `gender` CHAR(1) DEFAULT NULL,
  `email` VARCHAR(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```

> 导入`MyBatis`所需`jar`包

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>

<!-- 数据库连接 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.38</version>
</dependency>

<!-- 若需要输出 sql 日志则导入log4j -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.5</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.12</version>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

* 若需要输出 `sql` 日志则导入`log4j`
* 并在`src`中添加`xml`

> `log4j`.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">

<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">

<appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
	<param name="Encoding" value="UTF-8" />
    <layout class="org.apache.log4j.PatternLayout">
	<param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m  (%F:%L) \n" />
	</layout>
</appender>

<logger name="java.sql">
	<level value="debug" />
</logger>
<logger name="org.apache.ibatis">
	<level value="info" />
</logger>
<root>
	<level value="debug" />
    <appender-ref ref="STDOUT" />
</root>

</log4j:configuration>
```

> 编写`MyBatis`核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
<configuration>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url" value="jdbc:mysql://localhost:3306/my_batis" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!-- 在MyBatis总配置文件中导入 : Mapper -->
        <mapper resource="com/znsd/mybatis/bean/EmployeeMapper.xml" />
    </mappers>
</configuration>
```

> 定义封装表数据的类

```java
public class Employee {

	private Integer id;
	private String lastName;
	private String gender;
	private String email;

    Set(), Get();

	@Override
	public String toString();

}
```

> 为类编写配置`Mapper`文件用户存放一些`sql`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.znsd.mybatis.bean.EmployeeMapper">
	<select id="selectEmployee" resultType="com.znsd.mybatis.bean.Employee">
		SELECT `id`, `last_name` AS lastName, `gender`, `email` FROM `tbl_employee` WHERE `id` =  #{id}
	</select>
</mapper>
```

| 键                  | 描述                  |
| ------------------- | --------------------- |
| mapper : namespace  | 该文件的命名空间      |
| select : id         | 该条sql语句的唯一标识 |
| select : resultType | 返回值类型            |

> 编写测试类进行数据查询

```java
public class EmployeeTest {

	private SqlSessionFactory sqlSessionFactory;
	private SqlSession session = null;

	@Before
	public void before() throws IOException {
		String resource = "mybatis-config.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

		session = sqlSessionFactory.openSession();
	}

	@Test
	public void testSelectOne() {

		String sqlId = "com.znsd.mybatis.bean.EmployeeMapper.selectEmployee";
		Employee employee = session.selectOne(sqlId, 1);

		System.out.println(employee);

	}

	@After
	public void after() {
		session.close();
	}

}
```

1. `sqlId`的组成是由: `mapper : namespace`+`select : id`
2. 也可以直接使用: `select : id`来锁定`sql`语句

---

> 以上测试方法`MyBatis`以前使用的方式, 也可以说是: `IBatis`的使用方式

1. 定义`dao`层接口
2. 修改`mapper : namespace`为接口全类名
3. 修改`select : id`为接口的抽象方法
4. 定义单元测试进行使用

> 定义`dao`层接口

```java
public interface EmployeeMapper {

	public Employee getEmployeeById(Integer id);
	
}
```

> 1. 修改`mapper : namespace`为接口全类名
>
> 2. 修改`select : id`为接口的抽象方法

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace: 该文件的命名空间 : 写接口全类名 -->  
<mapper namespace="com.znsd.mybatis.dao.EmployeeMapper">
	
	<!-- select: 
			id : 该条sql语句的唯一标识 : 写接口中的抽象方法
			resultType : 返回值类型
	 -->
	<select id="getEmployeeById" resultType="com.znsd.mybatis.bean.Employee">
		SELECT `id`, `last_name` AS lastName, `gender`, `email` FROM `tbl_employee` WHERE `id` =  #{id}
	</select>
</mapper>
```

> 定义单元测试进行使用

```java
@Test
public void testGetEmployeeById() {

    EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);

    Employee employeeById = mapper.getEmployeeById(1);

    System.out.println(employeeById);

}
```

---

> #### 注意:

1. `SqlSession`代表和数据库的一次会话 : 用完必须关闭；
2. `SqlSession`和`connection`一样她都是非线程安全, 每次使用都应该去获取新的对象。
3. `mapper`接口没有实现类，但是mybatis会为这个接口生成一个代理对象。（将接口和xml进行绑定）
   1. `EmployeeMapper empMapper = sqlSession.getMapper(EmployeeMapper.class);`
4. 两个重要的配置文件：
   1. `mybatis`的全局配置文件：包含数据库连接池信息，事务管理器信息等...系统运行环境信息
   2. `sql`映射文件：保存了每一个sql语句的映射信息：将`sql`抽取出来。

