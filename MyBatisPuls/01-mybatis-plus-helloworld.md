> #### mybatis helloworld

> 在 mybatis 的基础上进行修改

> 对实体类进行改进

```java
/**
 * @TableName("tbl_employee")	: 该实体类对应的数据库表
 * @TableId(type = IdType.AUTO)	: 主键自增
 */
@TableName("tbl_employee")
public class Employee {

	@TableId(type = IdType.AUTO)
	private Integer id;

}
```

> 修改 spring 配置

```xml
<!-- mybatis session 工厂
	class="org.mybatis.spring.SqlSessionFactoryBean" > mybatis 
	class="com.baomidou.mybatisplus.spring.MybatisSqlSessionFactoryBean" > mybatisPlus
-->
<bean id="sessionFactory"
      class="com.baomidou.mybatisplus.spring.MybatisSqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="mapperLocations">
        <list>
            <value>classpath*:/com/znsd/mybatispuls/mapper/*.xml</value>
        </list>
    </property>
    <!-- 指定mybatis的配置文件(可有可无) -->
    <property name="configLocation" value="classpath:mybatis.xml"></property>
</bean>
```

> Dao 层接口 : BaseMapper<Employee> 中对应了一些普通的增删查改

```java
@Component
public interface EmployeeMapper extends BaseMapper<Employee> {

}
```

---

> 若数据库表名有一定的规范我们也可以不用在类中添加: 
>
> @TableName("tbl_employee")	: 该实体类对应的数据库表
>
> @TableId(type = IdType.AUTO)	: 主键自增`

> `spring-core.xml`

```xml
<bean id="sessionFactory"
		class="com.baomidou.mybatisplus.spring.MybatisSqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource" />
	<property name="mapperLocations">
		<list>
			<value>classpath*:/com/znsd/mybatispuls/mapper/*.xml</value>
		</list>
	</property>
	<!-- 指定mybatis的配置文件(可有可无) -->
	<property name="configLocation" value="classpath:mybatis.xml"></property>
	<property name="globalConfig" ref="globalConfiguration"></property>
</bean>
<!-- 配置实体类与数据库的规范 -->
<bean id="globalConfiguration" class="com.baomidou.mybatisplus.entity.GlobalConfiguration">
	<!-- 设置主键自增 -->
	<property name="idType" value="0"></property>
	<!-- 定义统一的数据库表规范 -->
	<property name="tablePrefix" value="tbl_"></property>
	<!-- 驼峰命名法 -->
	<property name="dbColumnUnderline" value="true"></property>
</bean>
```

> @TableField : 表字段标识

```java


public @interface TableField {

    /** 字段值（驼峰命名方式，该值可无）*/
    String value() default "";

    /** 当该Field为类对象时, 可使用#{对象.属性}来映射到数据表. */
    String el() default "";

    /** 是否为数据库表字段, 默认 true 存在，false 不存在 */
    boolean exist() default true;

    /** 字段 where 实体查询比较条件, 默认 `=` 等值 */
    String condition() default SqlCondition.EQUAL;

    /**
     * <p>
     * 字段 update set 部分注入, 该注解优于 el 注解使用
     * </p>
     * <p>
     * 例如：@TableField(.. , update="%s+1") 其中 %s 会填充为字段
     * 输出 SQL 为：update 表 set 字段=字段+1 where ...
     * </p>
     * <p>
     * 例如：@TableField(.. , update="now()") 使用数据库时间
     * 输出 SQL 为：update 表 set 字段=now() where ...
     * </p>
     */
    String update() default "";

    /** 字段验证策略, 默认 非 null 判断 */
    FieldStrategy strategy() default FieldStrategy.NOT_NULL;

    /** 字段自动填充策略 */
    FieldFill fill() default FieldFill.DEFAULT;
}
```

