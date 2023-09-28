> ### struts2返回 JSON 格式

#### *servlet 方式返回 JSON*

1. 方法不需要 **返回值** , 不需要配置 **result**
2. 获取 通过 response 获取 PrintWriter
3. 调用 write("JSON"); 方法输出 JSON
4. 配置 Action
5. 页面获取

```java
public void servletJson () throws IOException {
    HttpServletResponse response = ServletActionContext.getResponse();
    response.setCharacterEncoding("UTF-8");
    PrintWriter out = response.getWriter();

    ArrayList<Student> list = new ArrayList<Student>();
    list.add(new Student(1, "张三", "男"));
    list.add(new Student(2, "李四", "女"));
    list.add(new Student(3, "王五", "男"));
    
    out.write(JSON.toJSONString(list));

    out.close();
}
```

```xml
<action name="servletJson" class="com.znsd.struts2.action.ServletJson" method="servletJson">
</action>
```

```javascript
$.ajax({
    url:"servletJson",
    type:"post",
    dataType:"json",
    success:function(data) {
        console.log(data);
    }
});
```

**注意**

1. 在使用 PrintWriter 输出时注意 中文乱码问题
2. 返回是一个 String 类型需要前台去转换为 JSON 格式



#### *struts2 方式获取 JSON*

1. 导入 struts2-json-plugin-x.x.x.jar 注意要与 struts2-core-x.x.x.jar 版本保持一致
2. 编写 Action 类
3. 配置 Action 类
4. 页面获取

```java
public class Struts2Json {

    private List<Student> students;

    public String strutsJson () {

        students = new ArrayList<Student>();
        students.add(new Student(1, "张三", "男"));
        students.add(new Student(2, "李四", "女"));
        students.add(new Student(3, "王五", "男"));

        return "success";
    }

    Get(), Set();
    
}
```

```xml
<!-- struts2 返回json 包继承: json-default -->
<package name="default-json" extends="json-default" namespace="/">

    <action name="strutsJson" class="com.znsd.struts2.action.Struts2Json" method="strutsJson">
        <result type="json">
        	<!-- 指定转换的数据 -->
			<param name="root">students</param>
        </result>
    </action>

</package>
```

---

```xml
<!-- 还有几个未使用的参数 -->
<!-- 这里指定将被Struts2序列化的属性，该属性在action中必须有对应的getter方法 -->
<!-- 默认将会序列所有有返回值的getter方法的值，而无论该方法是否有对应属性 -->
<param name="root">dataMap</param>

<!-- 指定是否序列化空的属性 -->
<param name="excludeNullProperties">true</param>

<!-- 这里指定将序列化dataMap中的那些属性 -->
<param name="includeProperties">userList.*</param>

<!-- 取消浏览器缓存-->
<param name="noCache">true</param>

<!-- 设置服务器响应类型-->
<param name="contentType">application/json</param>

<!-- 这里指定将要从dataMap中排除那些属性，这些排除的属性将不被序列化，
	一般不与上边的参数配置同时出现 -->
<param name="excludeProperties">SUCCESS</param>
```

---

```javascript
$.ajax({
    url : "strutsJson",
    type : "post",
    success : function(data) {
        console.log(data);
    }
});
```

**注意**

1. 导入 struts2-json-plugin-x.x.x.jar 注意要与 struts2-core-x.x.x.jar 版本保持一致
2. struts2 返回json 包继承: json-default
3. 指定转换的数据 : <param name="root">students</param> , 若不指定将栈顶对象数据全部转换返回
4. 返回给前台的数据是 JSON 的无需转换格式