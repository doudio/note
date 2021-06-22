> ## SpringData Orcale10G

> 添加 SpringData 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>com.github.noraui</groupId>
    <artifactId>ojdbc8</artifactId>
    <version>12.2.0.1</version>
</dependency>
```

> 添加配置信息

```properties
spring.datasource.driverClassName=oracle.jdbc.driver.OracleDriver
spring.datasource.url=jdbc:oracle:thin:@192.168.0.238:1521:17h1
spring.datasource.username=test_17h1
spring.datasource.password=17h1

spring.jpa.show-sql=true
#spring.jpa.hibernate.naming-strategy=org.hibernate.cfg.ImprovedNamingStrategy
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.Oracle10gDialect
spring.jpa.properties.hibernate.hbm2ddl.auto=update
```

> `jdbc`:`oracle`:`thin(连接方式)`:@`192.168.0.238(地址)`:`1521(端口)`:`17h1(数据库)`
>
> | 连接方式 | 描述                                                         |
> | -------- | ------------------------------------------------------------ |
> | thin     | 不需要安装oracle客户端, 但它们在功能上并无差异。(常见)       |
> | oci      | oci(oracle called interface) 须在客户机上安装oracle客户端才能连接 |

> 编写实体类

```java
@Entity
@Table(name = "tb_user")
public class User {

    @Id
    @GeneratedValue
    private Integer id;
    private String name;
    private String pwd;

    // Setter(), Getter(), toString();
    
}
```

> 编写 SpringData `dao` 层接口

```java
@Repository
public interface UserDao extends JpaRepository<User, Integer> {
}
```

> 编写 SpringMVC `controller` 层

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserDao userDao;

    @GetMapping("/user")
    public List<User> fiandAll() {
        return userDao.findAll();
    }

}
```

