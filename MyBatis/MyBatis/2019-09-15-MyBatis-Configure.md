> #### MyBatis Configure

> #### `properties`: `mybatis` 
> 可以使用`properties`来引入外部`properties`配置文件的内容

| 字段     | 描述                             |
| -------- | -------------------------------- |
| resource | 引入类路径下的资源               |
| url      | 引入网络路径或者磁盘路径下的资源 |

```xml
<properties resource="jdbc.properties"></properties>
```

> #### `settings`包含很多重要的设置项

| 节点 OR 字段 | 描述                 |
| ------------ | -------------------- |
| setting      | 用来设置每一个设置项 |
| name         | 设置项名             |
| value        | 设置项取值           |

```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

| name                     | value          | defaultValue | describe                                                     |
| ------------------------ | -------------- | ------------ | ------------------------------------------------------------ |
| mapUnderscoreToCamelCase | `true` `false` | `false`      | 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。 |
| lazyLoadingEnabled       | `true false`   | `false`      | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置`fetchType`属性来覆盖该项的开关状态。 |

> 更多设置项: [官网文档](http://www.mybatis.org/mybatis-3/zh/configuration.html#settings)

> #### `typeAliases` 别名处理器：可以为我们的java类型起别名, 别名不区分大小写

| 节点 OR 字段      | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| typeAlias         | 为某个java类型起别名                                         |
| typeAlias : type  | 指定要起别名的类型全类名; 默认别名就是类名小写;              |
| typeAlias : alias | 指定新的别名                                                 |
| package           | 为某个包下的所有类批量起别名                                 |
| package : name    | 指定包名（为当前包以及下面所有的后代包的每一个类都起一个默认别名（类名小写） |

```xml
<typeAliases>
    <!-- <typeAlias type="com.znsd.mybatis.bean.Employee" alias="emp"/> -->

    <!-- <package name="com.znsd.mybatis.bean"/> -->
</typeAliases>
```

> **注意: 批量起别名的情况下，使用 `@Alias` 注解为某个类型指定新的别名**

> #### `environments`
>
> mybatis可以配置多种环境 ,default指定使用某种环境。可以达到快速切换环境。

| 节点 OR 字段       | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| environment        | 配置一个具体的环境信息；必须有一下两个标签；id代表当前环境的唯一标识 |
| transactionManager | 事务管理器                                                   |
| dataSource         | 数据源                                                       |

> transactionManager：事务管理器；
>
> ​	type：事务管理器的类型;JDBC(JdbcTransactionFactory)|MANAGED(ManagedTransactionFactory)
>
> ​		自定义事务管理器：实现`TransactionFactory`接口  `type`指定为全类名

> dataSource: 数据源
>
> ​	type:数据源类型;UNPOOLED(UnpooledDataSourceFactory)
> ​								|POOLED(PooledDataSourceFactory)
> ​								|JNDI(JndiDataSourceFactory)
> ​	自定义数据源：实现`DataSourceFactory`接口，`type`是全类名

```xml
<environments default="dev_mysql">
    <environment id="dev_mysql">
        <transactionManager type="JDBC"></transactionManager>
        <dataSource type="POOLED">
            <property name="driver" value="${jdbc.mysql.driver}" />
            <property name="url" value="${jdbc.mysql.url}" />
            <property name="username" value="${jdbc.mysql.username}" />
            <property name="password" value="${jdbc.mysql.password}" />
        </dataSource>
    </environment>

    <environment id="dev_oracle">
        <transactionManager type="JDBC" />
        <dataSource type="POOLED">
            <property name="driver" value="${jdbc.oracle.driver}" />
            <property name="url" value="${jdbc.oracle.url}" />
            <property name="username" value="${jdbc.oracle.username}" />
            <property name="password" value="${jdbc.oracle.password}" />
        </dataSource>
    </environment>
</environments>
```

> #### `databaseIdProvider`：支持多数据库厂商的；
>
> ​	type="DB_VENDOR"：VendorDatabaseIdProvider
> ​		作用就是得到数据库厂商的标识(驱动getDatabaseProductName())，
>
> ​		mybatis就能根据数据库厂商标识来执行不同的sql: `MySQL` `Oracle` `SQL Server` `xxxx`

```xml
<databaseIdProvider type="DB_VENDOR">
    <!-- 为不同的数据库厂商起别名 -->
    <property name="MySQL" value="mysql"/>
    <property name="Oracle" value="oracle"/>
    <property name="SQL Server" value="sqlserver"/>
</databaseIdProvider>
```

```xml
<mapper namespace="com.znsd.mybatis.dao.EmployeeMapper">
 	<select id="getEmpById" resultType="com.znsd.mybatis.bean.Employee">
		<!-- 默认情况下发送的sql -->
	</select>
	<select id="getEmpById" resultType="com.znsd.mybatis.bean.Employee"
		databaseId="mysql">
        <!-- mysql数据库下发送的sql -->
	</select>
	<select id="getEmpById" resultType="com.znsd.mybatis.bean.Employee"
		databaseId="oracle">
		<!-- oracle数据库下发送的sql -->
	</select>
</mapper>
```

> `select : databaseId` 指定不同的数据库厂商发送不同的`sql`语句
>
> 若不指定`select : databaseId` `sql`语句的匹配规则是从`精确`到`模糊`

> #### `mappers`将sql映射注册到全局配置中

| 节点 OR 字段      | 描述                                    |
| ----------------- | --------------------------------------- |
| mapper            | 注册一个sql映射                         |
| mapper : resource | 引用类路径下的sql映射文件               |
| mapper : url      | 引用网路路径或者磁盘路径下的sql映射文件 |
| mapper : class    | 引用（注册）接口, 通过注解来实现的接口  |
| package           | 批量注册                                |

```xml
<mappers>
    <!-- <mapper resource="com/znsd/mybatis/bean/EmployeeMapper.xml"/> -->
    <!-- <mapper class="com.znsd.mybatis.dao.EmployeeMapperAnnotation"/> -->

    <!-- 批量注册： -->
    <!-- <package name="com.znsd.mybatis.dao"/> -->
</mappers>
```

> com.znsd.mybatis.dao.`EmployeeMapperAnnotation` 接口

```java
public interface EmployeeMapperAnnotation {

	@Select("SELECT * FROM `tbl_employee` WHERE `id` =  #{id}")
	public Employee getEmployeeById(Integer id);
	
}
```

