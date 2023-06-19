> #### MyBatis TypeHandler

1. 编写类
   1. 实现`org.apache.ibatis.type.TypeHandler`接口
   2. 继承`org.apache.ibatis.type.BaseTypeHandler`类
2. 在`MyBatis`配置文件中配置
3. 在`ResultMap`中指定列引用类型转换器

> 编写类

```java
public class EmployeeTypeHandler implements TypeHandler<String> {

	public String getResult(ResultSet rs, String columnName) throws SQLException {
		String gender = rs.getString(3);
		return "0".equals(gender) ? "男" : "女";
	}
    
}
```

> 在`MyBatis`配置文件中配置

```xml
<typeHandlers>
    <package name="com.znsd.mybatis.handler"/>
</typeHandlers>
```

> 在`ResultMap`中指定列引用类型转换器

```xml
<resultMap type="com.znsd.mybatis.bean.Employee" id="myTypeHandler">
    <result column="gender" property="gender"
            typeHandler="com.znsd.mybatis.handler.EmployeeTypeHandler"/>
</resultMap>

<select id="findByEmpId" resultMap="myTypeHandler">
    SELECT * FROM `tbl_employee` WHERE `id` = #{id} 
</select>
```

---

> MySQL case when 使用

> 语法

```sql
case colume 
    when condition then result
    when condition then result
    when condition then result
else result
end
```

> 使用场景

```sql
SELECT `id`, `last_name`, (CASE `gender`
	WHEN '0' THEN '女'
	WHEN '1' THEN '男'
	ELSE '妖' END
) AS sex, `email` FROM `tbl_employee`
```

---

> 创建一个能批量操作的 `session`

```java
SqlSession session = sqlSessionFactory.openSession(ExecutorType.BATCH)
```

> `MyBatis` 调用存储过程

> 创建`mysql`存储过程: 插入一条记录返回自动增长的主键

```mysql
DELIMITER //
CREATE PROCEDURE insertGetAutoKey (`name` VARCHAR(10),OUT autoKey INT)
BEGIN
	INSERT INTO `tbl_department` (`name`) VALUES (`name`); 
	SELECT LAST_INSERT_ID() INTO autoKey;
END //
```

> 问一: mybatis 如何调用存储过程拿到这个自动增长的主键

> 编写: `select`标签

```xml
<select id="saveDep" statementType="CALLABLE">
    {CALL insertGetAutoKey(
    	#{dep.name,mode=IN,jdbcType=VARCHAR}, 
    	#{dep.id,mode=OUT,jdbcType=INTEGER}) }
</select>
```

> 调用存储过程

```java
DepartmentMapper mapper = session.getMapper(DepartmentMapper.class);
		
Department department = new Department("depName");
mapper.saveDep(department);

System.out.println(department);
```

> 问二: 如果我在mybatis mapper xml 配置文件中想使用 > < 这种运算符应该这么办

```xml
<![CDATA[SELECT * FROM TABLE]]>
```

> 使用

```xml
<select id="findByIdBetween" resultType="com.znsd.mybatis.bean.Department">
    <![CDATA[SELECT * FROM `tbl_department` WHERE `id` > #{minId} AND `id` < #{maxId}]]>
</select>
```

