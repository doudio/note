> #### SpringMVC-ReturnJSON

1. 导入`jar ` `:` `jackson-databind`
2. 编写目标方法
3. 编写`ajax`请求获取数据

> 导入`jar ` `:` `jackson-databind`

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.5</version>
</dependency>
```

> 编写目标方法

```java
/**
 * 返回 JSON 格式
 */
@ResponseBody
@RequestMapping("returnJSON")
public Collection<Student> returnJSON () {
    Collection<Student> jsonData = new ArrayList<Student>();
    jsonData.add(new Student("张三", "男", 110));
    jsonData.add(new Student("李四", "女", 120));
    jsonData.add(new Student("王五", "男", 130));
    jsonData.add(new Student("小明", "男", 119));
    return jsonData;
}
```

1. 将需要放回的数据作为`返回值`
2. 并在方法上添加 `@ResponseBody` 注解
3. 若报错在SpringMVC中添加: `<mvc:annotation-driven />`

> 编写`ajax`请求获取数据

```js
$.get("returnJSON", function(data) {
	var str = "";
	for (var i = 0; i < data.length; i++) {
		str += "<li>" + data[i].name + "</li>";
	}
	$("ul").empty().append(str);
});
```

> #### 文件上传

1. 导入所需`jar`
2. 在 SpringMVC 核心配置文件中进行配置
3. 编写目标方法
4. 编写页面发起请求

> 导入所需`jar`

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

> 在 SpringMVC 核心配置文件中进行配置

```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <property name="defaultEncoding" value="UTF-8"></property>
    <property name="maxUploadSize" value="1024000"></property>
</bean>
```

> 编写目标方法

```java
/**
 * 文件上传
 */
@RequestMapping("upload")
public String upload(@RequestParam("desc") String desc, @RequestParam("file") MultipartFile file) throws IOException {
    System.out.println("desc: " + desc);
    System.out.println("OriginalFilename: " + file.getOriginalFilename());
    System.out.println("InputStream: " + file.getInputStream());
    return "success";
}
```

> 编写页面发起请求

```html
<form action="upload" method="post" enctype="multipart/form-data">
    File: <input type="file" name="file" /><br />
    Desc: <input type="text" name="desc" /><br />
    <input type="submit" />
</form>
```

> ### 文件下载

1. 编写目标方法该方法返回值为: `ResponseEntity<byte[]>`
2. 获取下载文件的数据
3. 设置响应头
4. 设置响应码
5. 页面发起请求

```java
@RequestMapping("download")
public ResponseEntity<byte[]> download (HttpSession session) throws IOException {
    // 获取下载文件的数据
    ServletContext servletContext = session.getServletContext();
    InputStream resourceAsStream = servletContext.getResourceAsStream("/download/img.jpg");
    byte[] body = new byte[resourceAsStream.available()];
    resourceAsStream.read(body);

    // 设置响应头
    HttpHeaders headers = new HttpHeaders();
    headers.add("Content-Disposition", "attchment;filename=download.jpg");

    // 设置返回状态码
    HttpStatus statusCode = HttpStatus.OK;

    ResponseEntity<byte[]> response = new ResponseEntity<byte[]>(body, headers, statusCode);
    return response;
}
```

> 页面发起请求

```html
<a href="download">download</a>
```

