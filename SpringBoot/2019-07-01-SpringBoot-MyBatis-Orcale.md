> ## SpringBoot MyBatis Orcale

> 导入 `mybatis` 和 `orcale`

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.1.1</version>
</dependency>

<dependency>
    <groupId>com.github.noraui</groupId>
    <artifactId>ojdbc8</artifactId>
    <version>12.2.0.1</version>
</dependency>
```

> 在 `application.properties` 中配置

```properties
spring.datasource.driverClassName=oracle.jdbc.driver.OracleDriver
spring.datasource.url=jdbc:oracle:thin:@ip:1521:dbname
spring.datasource.username=username
spring.datasource.password=password

# mybatis 外部xml路径
mybatis.mapperLocations=classpath:mapper/*.xml
# mybatis 实体类
mybatis.type-aliases-package=com.znsd.ajz.beans
# mybatis 驼峰命名
mybatis.configuration.map-underscore-to-camel-case=true
```

> `jdbc`:`oracle`:`thin(连接方式)`:@`192.168.0.238(地址)`:`1521(端口)`:`17h1(数据库)`
>
> | 连接方式 | 描述                                                         |
> | -------- | ------------------------------------------------------------ |
> | thin     | 不需要安装oracle客户端, 但它们在功能上并无差异。(常见)       |
> | oci      | oci(oracle called interface) 须在客户机上安装oracle客户端才能连接 |

> 在`SpringBoot`启动类上添加

```java
@SpringBootApplication
@MapperScan("com.znsd.ajz.repository")
public class Application {}
```

> `Mapper.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="package.Object"></mapper>
```

> ### 接下来分别创建`beans` `controller` `service` `dao`层进行使用