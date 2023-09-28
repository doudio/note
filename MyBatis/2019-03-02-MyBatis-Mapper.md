> #### MyBatis Mapper

> 定义简单的`c` `r` `u` `d`

> 类数据接口: `EmployeeMapper`

```java
public interface EmployeeMapper {

	public boolean insertEmployee(Employee employee);
	
	public boolean deleteEmployee(Integer id);
	
	public Employee getEmployeeById(Integer id);
	
	public boolean updateEmployee(Employee employee);
	
}
```

> `EmployeeMapper.xml`配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.znsd.mybatis.dao.EmployeeMapper">
    
	<!-- public boolean insertEmployee(Employee employee); -->
	<insert id="insertEmployee">
		INSERT INTO `tbl_employee` (`last_name`, `gender`, `email`) 
        VALUE(#{lastName}, #{gender}, #{email})
	</insert>
	
	<!-- public boolean deleteEmployee(Integer id); -->
	<delete id="deleteEmployee">
		DELETE FROM `tbl_employee` WHERE `id` = #{id}
	</delete>
	
	<!-- public Employee getEmployeeById(Integer id); -->
	<select id="getEmployeeById" resultType="com.znsd.mybatis.bean.Employee">
		SELECT * FROM `tbl_employee` WHERE `id` = #{id}
	</select>
	
	<!-- public boolean updateEmployee(Employee employee); -->
	<update id="updateEmployee">
		UPDATE `tbl_employee` SET 
        	`last_name` = #{lastName}, `gender` = #{gender}, `email` = #{email} 
        WHERE `id` = #{id}
	</update>
	
</mapper>
```

> 注意: 

1. 除查询意外的其他标签可以省略 `resultType`

> 编写测试类

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
	public void testInsert() {
		EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
		Employee employee = new Employee(null, "lastName", "0", "lastName@qq.com");
		
		boolean insertEmployee = mapper.insertEmployee(employee);
		System.out.println(insertEmployee);
	}
	
	@Test
	public void testDelete() {
		EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
		boolean deleteEmployee = mapper.deleteEmployee(5);
		System.out.println(deleteEmployee);
	}
	
	@Test
	public void testSelect() {
		EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
		Employee employeeById = mapper.getEmployeeById(1);
		System.out.println(employeeById);
	}
	
	@Test
	public void testUpdate() {
		EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
		Employee employee = new Employee(6, "lastName-update", "0", "lastName@qq.com");
		boolean updateEmployee = mapper.updateEmployee(employee);
		System.out.println(updateEmployee);
	}
	

	@After
	public void after() {
		session.commit();
		session.close();
	}

}
```

> 注意: 

1. 增加若没有提交事务还会返回true

---

> 插入成功获取自动增长的主键

1. 在`mapper`文件中修改
2. 插入成功后输出对象

> 在`mapper`文件中修改

```xml
<insert id="insertEmployee" parameterType="com.znsd.mybatis.bean.Employee" useGeneratedKeys="true" keyProperty="id" databaseId="mysql">
	INSERT INTO `tbl_employee` (`last_name`, `gender`, `email`) 
   	VALUE(#{lastName}, #{gender}, #{email})
</insert>
```

> 获取自增主键的值：

1. `mysql`支持自增主键，自增主键值的获取，`mybatis`也是利用`statement.getGenreatedKeys()；`
2. `useGeneratedKeys="true"`使用自增主键获取主键值策略
3. `keyProperty`指定对应的主键属性，也就是`mybatis`获取到主键值以后，将这个值封装给javaBean的哪个属性

```xml
<!-- 
 获取非自增主键的值：
  Oracle不支持自增；Oracle使用序列来模拟自增；
  每次插入的数据的主键是从序列中拿到的值；如何获取到这个值；
  -->
<insert id="addEmp" databaseId="oracle">
    <!-- 
      keyProperty:查出的主键值封装给javaBean的哪个属性
      order="BEFORE":当前sql在插入sql之前运行
          AFTER：当前sql在插入sql之后运行
      resultType:查出的数据的返回值类型

      BEFORE运行顺序：
       先运行selectKey查询id的sql；查出id值封装给javaBean的id属性
       在运行插入的sql；就可以取出id属性对应的值
      AFTER运行顺序：
       先运行插入的sql（从序列中取出新值作为id）；
       再运行selectKey查询id的sql；
   -->
    <selectKey keyProperty="id" order="BEFORE" resultType="Integer">
        <!-- 编写查询主键的sql语句 -->
        <!-- BEFORE-->
        select EMPLOYEES_SEQ.nextval from dual 
        <!-- AFTER：
    select EMPLOYEES_SEQ.currval from dual -->
    </selectKey>

    <!-- 插入时的主键是从序列中拿到的 -->
    <!-- BEFORE:-->
    insert into employees(EMPLOYEE_ID,LAST_NAME,EMAIL) 
    values(#{id},#{lastName},#{email<!-- ,jdbcType=NULL -->}) 
    <!-- AFTER：
  insert into employees(EMPLOYEE_ID,LAST_NAME,EMAIL) 
  values(employees_seq.nextval,#{lastName},#{email}) -->
</insert>
```

> 插入成功后输出对象

```java
@Test
public void testInsert() {
    EmployeeMapper mapper = session.getMapper(EmployeeMapper.class);
    Employee employee = new Employee(null, "lastName", "0", "lastName@qq.com");

    boolean insertEmployee = mapper.insertEmployee(employee);
    System.out.println(insertEmployee);
    System.out.println(employee);
}
```

----

> `MyBatis` 参数处理

> 单个参数：`mybatis`不会做特殊处理:  #{`参数名`/`任意名`}：取出参数值。

> 多个参数：`mybatis`会做特殊处理。
> ​	多个参数会被封装成 一个`map`，
> ​		key：`param1...paramN`或者参数的索引也可以
> ​		value：传入的参数值
> ​	#{}就是从map中获取指定的key的值； ​	

```java
异常：
	org.apache.ibatis.binding.BindingException: 
	Parameter 'id' not found. 
	Available parameters are [1, 0, param1, param2]
	操作：
		方法：public Employee getEmpByIdAndLastName(Integer id,String lastName);
		取值：#{id},#{lastName}
```

> 【命名参数】：明确指定封装参数时map的key；@Param("id")
> ​	多个参数会被封装成 一个map，
> ​		key：使用@Param注解指定的值
> ​		value：参数值
> ​	#{指定的key}取出对应的参数值

```java
public Employee getEmpByIdAndLastName(@Param("id") Integer id,
                                      @Param("lastName")String lastName);
```

> POJO：
> 如果多个参数正好是我们业务逻辑的数据模型，我们就可以直接传入pojo；
> ​	#{属性名}：取出传入的pojo的属性值	

> Map：
> 如果多个参数不是业务模型中的数据，没有对应的pojo，不经常使用，为了方便，我们也可以传入map
> ​	#{key}：取出map中对应的值

>  TO：
> 如果多个参数不是业务模型中的数据，但是经常要使用，推荐来编写一个TO（Transfer Object）数据传输对象

```java
public class Page{
	Integer page;
	Integer limit;
}
```

---

>  #### 思考: 	

> public Employee getEmp(@Param("id")Integer id,String lastName);
> ​	取值：id=>#{id/param1}   lastName=>#{param2}

>  public Employee getEmp(Integer id,@Param("e")Employee emp);
> ​	取值：id=>#{param1}    lastName=>#{param2.lastName/e.lastName}

>  `特别注意`：如果是Collection（List、Set）类型或者是数组，
> ​		 也会特殊处理。也是把传入的list或者数组封装在map中。
> ​			key：Collection（collection）,如果是List还可以使用这个key(list)
> ​				数组(array)
> public Employee getEmpById(List<Integer> ids);
> ​	取值：取出第一个id的值：   #{list[0]}​	

> 参数值的获取
>
> 1. #{}：可以获取map中的值或者pojo对象属性的值；
> 2. ${}：可以获取map中的值或者pojo对象属性的值；

```sql
select * from tbl_employee where id=${id} and last_name=#{lastName}
Preparing: select * from tbl_employee where id=2 and last_name=?
区别：
	#{}:是以预编译的形式，将参数设置到sql语句中；PreparedStatement；防止sql注入
	${}:取出的值直接拼装在sql语句中；会有安全问题；
	大多情况下，我们去参数的值都应该去使用#{}；

	原生jdbc不支持占位符的地方我们就可以使用${}进行取值
    	比如分表、排序。。。；按照年份分表拆分
			select * from ${year}_salary where xxx;
            select * from tbl_employee order by ${f_name} ${order}
```

>  #{}:更丰富的用法：
> ​	规定参数的一些规则：
> ​	javaType、 jdbcType、 mode（存储过程）、 numericScale、
> ​	resultMap、 typeHandler、 jdbcTypeName、 expression（未来准备支持的功能）；

```xml
jdbcType通常需要在某种特定的条件下被设置：
	在我们数据为null的时候，有些数据库可能不能识别mybatis对null的默认处理。比如Oracle（报错）；
	
	JdbcType OTHER：无效的类型；因为mybatis对所有的null都映射的是原生Jdbc的OTHER类型，oracle不能正确处理;
	
	由于全局配置中：jdbcTypeForNull=OTHER；oracle不支持；两种办法
	1、#{email,jdbcType=OTHER};
	2、jdbcTypeForNull=NULL
		<setting name="jdbcTypeForNull" value="NULL"/>
```
