> #### MyBatis Mapper

> 将查询结果封装`list`集合

```xml
<!-- public List<Employee> getEmployeeList(); -->
<select id="getEmployeeList" resultType="com.znsd.mybatis.bean.Employee">
    SELECT * FROM `tbl_employee`
</select>
```

> 将查询结果封装`map`

```xml
<!-- public Map<String, Object> getEmployeeMap(); -->
<select id="getEmployeeMap" resultType="map">
    SELECT * FROM `tbl_employee` WHERE `id` = 1
</select>
```

>  OR

```xml
<!-- @MapKey("id")
 public Map<Integer, Employee> getEmployeeMaps(); -->
<select id="getEmployeeMaps" resultType="com.znsd.mybatis.bean.Employee">
    SELECT * FROM `tbl_employee`
</select>
```

> `resultMap`:自定义封装结果

```xml
<resultMap type="com.znsd.mybatis.bean.Employee" id="empResultMap">
    <id column="id" property="id"/>
    <result column="email" property="email" />
</resultMap>

<!-- public Employee getResultMapNode(Integer id); -->
<select id="getResultMapNode" resultMap="empResultMap">
    SELECT * FROM `tbl_employee` WHERE `id` = #{id}
</select>
```

* `resultType` : 与 `resultMap`只能二选一
* `id`属性最好使用`id`标签来指定封装的字段, 底层有封装

> 联合查询：级联属性封装结果集	

```xml
<resultMap type="com.znsd.mybatis.bean.Employee" id="employeeMap">
    <id column="empid" property="id"/>
    <result column="last_name" property="lastName"/>
    <result column="depid" property="department.id"/>
    <result column="name" property="department.name"/>
</resultMap>

<!--  public Employee getEmpAndDept(Integer id);-->
<select id="getEmpAndDept" resultMap="employeeMap">
    SELECT 
		emp.`id` AS empid, dep.`id` depid, emp.`last_name`, dep.`name`
    FROM `tbl_employee` AS emp
    INNER JOIN `tbl_department` AS dep ON emp.`dep_id` = dep.`id`
    WHERE emp.`id` = 1
</select>
```

> OR 使用`association`定义关联的单个对象的封装规则；

```xml
<resultMap type="com.znsd.mybatis.bean.Employee" id="employeeMap">
    <id column="empid" property="id"/>
    <result column="last_name" property="lastName"/>
    
    <association property="department" javaType="com.znsd.mybatis.bean.Department">
        <id column="did" property="id"/>
        <result column="name" property="name"/>
    </association>
</resultMap>
```

* `association`可以指定联合的javaBean对象
* `property="department"`：指定哪个属性是联合的对象
* `javaType`:指定这个属性对象的类型[不能省略]

> OR 使用`association`进行分步查询

```xml
<resultMap type="com.znsd.mybatis.bean.Employee" id="employeeMap">
    <id column="id" property="id"/>
    <result column="last_name" property="lastName"/>
    
    <association property="dept" 
		select="com.znsd.mybatis.dao.DepartmentMapper.getDeptById"
		column="d_id">
    </association>
</resultMap>
```

1. 先按照员工`id`查询员工信息
2. 根据查询员工信息中的`depId`值去部门表查出部门信息
3. 部门设置到员工中
4. `association`定义关联对象的封装规则
5. `select`表明当前属性是调用select指定的方法查出的结果
6. `column`指定将哪一列的值传给这个方法
7. 使用select指定的方法（传入column指定的这列参数的值）查出对象，并封装给`property`指定的属性

---

> 嵌套结果集的方式，使用collection标签定义关联的集合类型的属性封装规则

```xml
<resultMap type="com.znsd.mybatis.bean.Department" id="department">
    <id column="depId" property="id"/>
    <result column="name" property="name"/>
    
    <collection property="emps" ofType="com.znsd.mybatis.bean.Employee">
        <!-- 定义这个集合中元素的封装规则 -->
        <id column="eid" property="id"/>
        <result column="last_name" property="lastName"/>
        <result column="email" property="email"/>
        <result column="gender" property="gender"/>
    </collection>
</resultMap>
```

1. `collection`定义关联集合类型的属性的封装规则
2. `ofType`指定集合里面元素的类型

> OR `collection：分段查询`

```xml
<resultMap type="com.znsd.mybatis.bean.Department" id="departmentMap">
    <id column="id" property="id"/>
    <id column="dept_name" property="departmentName"/>
    <collection property="emps" 
                select="com.znsd.mybatis.dao.EmployeeMapper.getEmpsByDeptId"
                column="{deptId=id}" fetchType="lazy"></collection>
</resultMap>
```

> 配置的接口方法

```java
<!-- public Department getDeptByIdStep(Integer id); -->
<select id="getDeptByIdStep" resultMap="departmentMap">
    select id,dept_name from tbl_dept where id=#{id}
</select>
```

> 扩展：多列的值传递过去：

1. 将多列的值封装map传递；
   1. `column`= `"{key1=column1,key2=column2}" `
2. `fetchType`=`"lazy"`：表示使用延迟加载；
   1. `lazy`：延迟
   2. `eager`：立即

---

> `<discriminator javaType=""></discriminator>`:鉴别器

```xml
<resultMap type="com.znsd.mybatis.bean.Employee" id="employeeMap">
    <id column="id" property="id"/>
    <result column="last_name" property="lastName"/>
    <result column="email" property="email"/>
    <result column="gender" property="gender"/>
    <!--
        column：指定判定的列名
        javaType：列值对应的java类型
	-->
    <discriminator javaType="string" column="gender">
        <!-- resultType:指定封装的结果类型；不能缺少。-->
        <case value="true|false" resultType="com.atguigu.mybatis.bean.Employee">
            <association property="dept" 
                         select="com.atguigu.mybatis.dao.DepartmentMapper.getDeptById"
                         column="d_id">
            </association>
        </case>

        <case value="true|false" resultType="com.atguigu.mybatis.bean.Employee">
            <id column="id" property="id"/>
            <result column="last_name" property="email"/>
            <result column="gender" property="gender"/>
        </case>
    </discriminator>
</resultMap>
```

* 鉴别器：mybatis可以使用discriminator判断某列的值，然后根据某列的值改变封装行为