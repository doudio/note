> #### SpringMVC Servlet API

```java
/**
 * 注入 Servlet API 
 */
@RequestMapping("servletAPI")
public void servletAPI (HttpServletRequest request, 
                        HttpServletResponse response, 
                        Writer out) throws IOException {

    System.out.println("servletAPI(" + request + ", " + response + ", " + out + ")");
	out.write("success");
}
```

> 可以注入

1. HttpServletRequest
2. HttpServletResponse
3. HttpSession
4. Writer
5. Map<`String`, `Object`>
6. Reader
7. InputStream
8. OutputStream
9. java.security.Principal
10. Locale

> #### 将值放入 Request 与 Session 中

```java
@Controller
@SessionAttributes(value = {"now", "key"}, types = {Integer.class})
public class ParamDemo {

	private static final String SUCCESS = "success";
		
	@RequestMapping("sessionMap")
	public String sessionMap (Map<String, Object> map) {
		map.put("now", LocalDateTime.now());
		map.put("key", "value");
		map.put("integer", 110);
		return SUCCESS;
	}
	
}
```

> 页面获取

```html
<ol>
    <li>${requestScope.now }</li>
    <li>${sessionScope.now }</li>
    <li>${sessionScope.key }</li>
    <li>${sessionScope.integer }</li>
</ol>
```

> #### @SessionAttributes

```java
public @interface SessionAttributes {

	/**
	 * Map 中那些值需要放在 Session 中的
	 */
	String[] value() default {};

	/**
	 * Map 中那些类型需要放在 Session 中
	 */
	Class<?>[] types() default {};

}
```

> #### @ModelAttribute

```java
@ModelAttribute
public void getUser(@RequestParam(value = "id", required = false) Integer id, 
			Map<String, Object> map) {
    System.out.println("getUser(Integer " + id + ")");
    if (id != null) {
        Student student = new Student(id, "name", "男", 110);
        System.out.println(student);
        map.put("student", student);
    }
}

@RequestMapping("updateStudent")
public String updateStudent(Student student) {
    System.out.println("updateStudent(Student " + student + ")");
    return SUCCESS;
}
```

1. 添加注解后会在该类中的其他方法执行前调用 , 若将值放入`map`中放入的对象的值会赋值给其他方法的参数
2. 注意他与 @SessionAttributes 会出现: 
   1. **Message** Session attribute 'student' required - not found in session
   2. 解决将需要放入 session 中的对象 name or type 修改掉
   3. 解决 添加一个 @ModelAttribute 修饰的方法

```java
@RequestMapping("modelAttr")
public String modelAttr(@ModelAttribute("abc") Student student) {
    System.out.println("updateStudent(Student " + student + ")");
    return SUCCESS;
}
```

> 页面获取值

```jsp
<li>${requestScope.abc }</li>
```

