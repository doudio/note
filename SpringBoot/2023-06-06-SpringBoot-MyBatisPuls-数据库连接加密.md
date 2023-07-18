## SpringBoot MyBatisPuls 数据库连接加密

### 在项目中添加依赖

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency><!--MySql 连接-->

<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.0.6</version>
</dependency><!--mybatis-plus 依赖-->

<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>2.1.0</version>
</dependency><!--加密工具-->
```

### 执行 `jastpt` 对数据库进行加密

#### 在 Maven 仓库中找到 jasypt org\jasypt\jasypt\ 

```shell
java -cp jasypt-X.x.x.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI input="root" password="PWDSalt" algorithm=PBEWithMD5AndDES
```

##### input : 密码

##### password : 盐

##### algorithm ： 加密方式

#### 执行后结果

```shell
----ENVIRONMENT-----------------

Runtime: Oracle Corporation Java HotSpot(TM) 64-Bit Server VM 25.201-b09



----ARGUMENTS-------------------

algorithm: PBEWithMD5AndDES
input: root
password: PWDSalt



----OUTPUT----------------------

78BzLEQrDvBnBHFVOp0ciQ==
```

### 配置数据库连接

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql:///test?characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2B8
    username: ENC(uCOaGYasrPM7P76GL6BRvQ==)
    password: ENC(uCOaGYasrPM7P76GL6BRvQ==)
    
jasypt:
  encryptor:
    password: PWDSalt
#    property:
#      prefix: PRE(     自定义前缀:默认 ENC(
#      suffix: $		自定义后缀:默认 )
```

### 添加mybatis-plus配置

```yaml
mybatis-plus:
  #配置mybatis-plus xml 路径
  mapper-locations: classpath:/mapper/**/*Mapper.xml
  #实体类别名, 多个package用[ , 或 ; ]区分
  typeAliasesPackage: package.*.benas
  global-config:
    refresh:  true
    db-config:
      id-type: auto
      #字段策略 0:"忽略判断",1:"非 NULL 判断"),2:"非空判断"
      field-strategy: ignored
      #mp2.3+ 全局表前缀 mp_
      #table-prefix: zss_
      #数据库大写下划线转换
      capital-mode: true
      #逻辑删除配置（下面3个配置）
      logic-delete-value: 1
      logic-not-delete-value: 0
      db-type: mysql
    #自定义填充策略接口实现
    #meta-object-handler: com.baomidou.springboot.MyMetaObjectHandler
  configuration:
    #配置驼峰命名规则
    map-underscore-to-camel-case: true
    cache-enabled: false
    #配置JdbcTypeForNull, oracle数据库必须配置
    jdbc-type-for-null: 'null'
```

### 编写配置类

```java
@Configuration
// 启注解事务管理，等同于xml配置方式的 <tx:annotation-driven />
@EnableTransactionManagement
@MapperScan(basePackages = "package.mappers")// mapper 路径
public class MybatisPlusConfig {}
```

### 编写表实体类

```java
@Data
@TableName("table_name")
public class TableName {

    @TableId
    private Integer id;

    private String name;
    private String field;

}
```

### 编写mapper

```java
public interface TableNameMapper extends BaseMapper<TableName> {
}
```

### 编写Controller进行查询

```java
@RestController
@RequestMapping("/table")
public class TableNameController {

    @Autowired
    private TableNameMapper mapper;

    @GetMapping("/")
    public List<TableName> findAll() {
        return mapper.selectList(null);
    }

}
```

