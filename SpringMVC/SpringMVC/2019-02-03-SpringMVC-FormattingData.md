> #### SpringMVC Formatting Data


> 配置annotation-driven

```xml
<mvc:annotation-driven ></mvc:annotation-driven>
```

> 使用 格式化注解

```java
@NumberFormat(pattern="#,###,###.#")
private Float price;

@DateTimeFormat(pattern="yyyy-MM-dd")
private Date productionDate;
```

> JSR 303

| **Constraint**                | **详细信息**                                             |
| ----------------------------- | -------------------------------------------------------- |
| `@Null`                       | 被注释的元素必须为 `null`                                |
| `@NotNull`                    | 被注释的元素必须不为 `null`                              |
| `@AssertTrue`                 | 被注释的元素必须为 `true`                                |
| `@AssertFalse`                | 被注释的元素必须为 `false`                               |
| `@Min(value)`                 | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 |
| `@Max(value)`                 | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值 |
| `@DecimalMin(value)`          | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值 |
| `@DecimalMax(value)`          | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值 |
| `@Size(max, min)`             | 被注释的元素的大小必须在指定的范围内                     |
| `@Digits (integer, fraction)` | 被注释的元素必须是一个数字，其值必须在可接受的范围内     |
| `@Past`                       | 被注释的元素必须是一个过去的日期                         |
| `@Future`                     | 被注释的元素必须是一个将来的日期                         |
| `@Pattern(value)`             | 被注释的元素必须符合指定的正则表达式                     |

> Hibernate Validator 扩展注

| **Constraint** | **详细信息**                           |
| -------------- | -------------------------------------- |
| `@Email`       | 被注释的元素必须是电子邮箱地址         |
| `@Length`      | 被注释的字符串的大小必须在指定的范围内 |
| `@NotEmpty`    | 被注释的字符串的必须非空               |
| `@Range`       | 被注释的元素必须在合适的范围内         |

> SpringMVC 表单标签

```html
<form:form action="${pageContext.request.contextPath }/emp" method="POST" modelAttribute="employee">
    <!-- path:对应 input > name -->
    LastName: <form:input path="lastName" />
    <br />
    Email: <form:input path="email" />
    <br />
    <%
       Map<String, String> genders = new HashMap<String, String>();
    genders.put("1", "Male");
    genders.put("0", "Female");

    request.setAttribute("genders", genders);
    %>
    Genders: <br /><form:radiobuttons path="gender" items="${genders }" 
                                      delimiter="<br />"/>
    <br />
    <form:select path="department.id" items="${departments }" itemLabel="departmentName" itemValue="id"></form:select>

    <br />
    <input type="submit" />
</form:form>
```

> 注意:

1. 可以通过 modelAttribute 属性指定绑定的模型属性
2. 若没有指定该属性，则默认从 request 域对象中读取 command 的表单 bean 如果该属性值也不存在，则会发生错误。
3. fomr 提交 500 : 页面 input 导致没有正确的像方法参数赋值

> #### 导入静态资源 `jquery` 404 问题解决方案

```xml
<!-- 将静态资源处理权交给 Tomcat -->
<mvc:default-servlet-handler />

<mvc:annotation-driven></mvc:annotation-driven>
```

1. 在有时候我们访问静态资源的时候 SpringMVC 会抛出没有这个映射
2. 这时候我们可以添加: 	`<mvc:default-servlet-handler />`注解来解决这个问题
3. 但是添加注解后又导致访问方法有不好用了这时候添加: `<mvc:annotation-driven></mvc:annotation-driven>` 注解来解决

---

> 使用 Hibernate Validator 扩展注解

1. 导入 `jar`
2. 编写方法: 获取数据 添加注解
3. 并获取字段错误信息

> 导入 `jar`

```xml
<dependency>
	<groupId>org.hibernate</groupId>
	<artifactId>hibernate-validator</artifactId>
	<version>6.0.12.Final</version>
</dependency>
```

> 编写方法: 获取数据 添加注解, 并获取字段错误信息

```java
/**
 * @param employee
 * @param result 若字段约束出现问题将错误信息放入 BindingResult 中
 * @return
 */
@RequestMapping(value = "/emp", method = RequestMethod.POST)
public String save(@Valid Employee employee, BindingResult result, Map<String, Object> map) {
	System.out.println("employee:" + employee);
	if (result.getErrorCount() > 0) {
		for (FieldError error : result.getFieldErrors()) {
			System.out.println(error.getField() + ":" + error.getDefaultMessage());
		}
		map.put("departments", departmentDao.getDepartments());
		return "input";
	}
	employeeDao.save(employee);
	return "redirect:/emps";
}
```

> #### 在页面输出字段错误消息

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>

<!-- 显示错误消息: path = * , 表示显示所有错误消息 -->
<form:errors path="*"></form:errors>
<!-- 单独输出莫一个字段的错误信息 -->
<form:errors path="field"></form:errors>
```

