> 整合mybatis进行开发

> 导入`mybatis` `and` `mysql-connector`

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.1</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

> 配置数据库连接必要参数

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/springboot_test
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

> 若在运行时抛出

```java
java.sql.SQLException: The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.
```

> 则在: `spring.datasource.url` 后面添加连接参数: 
>
> ```properties
> ?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
> ```

> 编写`mybatis` `dao` 层

```java
@Mapper
public interface StudentDao {

	@Select("SELECT `id`, `name`, `gender` FROM `tbl_student`")
	public List<Student> fandStudents();
	
}
```

> 使用`xml`描述`sql` 多添加一条配置: `mybatis.mapper-locations=classpath:mapper/*.xml`

> 整合mybatis分页插件

> 导入 `pagehelper` 场景启动器

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.5</version>
</dependency>
```

> 配置 `pagehelper` 参数

```properties
pagehelper.helperDialect=mysql
pagehelper.reasonable=true
pagehelper.supportMethodsArguments=true
pagehelper.params=count=countSql
pagehelper.page-size-zero=true
```

