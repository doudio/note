> #### SpringMVC 注解

> #### @RequestMapping

```java
public @interface RequestMapping {

	/**
	 * 请求的 URL
	 */
	String[] value() default {};

	/**
	 * 请求方式 
	 * 	RequestMethod.POST
	 * 	GET		: 查询
 	 * 	POST	: 增加
 	 * 	PUT		: 更新
 	 * 	DELETE	: 删除 
	 *  1. 配置过滤器: HiddenHttpMethodFilter
	 *  2. 编写 POST 表单中包含: name="_method" value="请求方式"
	 */
	RequestMethod[] method() default {};

	/**
	 * 请求参数 params = { "name","age!=110"}
	 */
	String[] params() default {};

	/**
	 * 请求头 headers = { "Host=localHost:8080"}
	 */
	String[] headers() default {};

	/**
	 * 格式是单个媒体类型或媒体类型序列，
	 * 如果内容类型与这些媒体类型中的一种匹配，
	 * 则仅映射映射请求。实例：
	 * 	consumes = "text/plain"
	 * 	consumes = {"text/plain", "application/*"}
	 */
	String[] consumes() default {};

	/**
	 * 格式是单个媒体类型或一系列媒体类型，只有当Accept匹配这些媒体类型之一时才映射请求。实例：
	 * 	produces = "text/plain"
	 * 	produces = {"text/plain", "application/*"}
	 */
	String[] produces() default {};

}
```

* 使用方式

```java
@RequestMapping(value = "/mappingWhere", method = RequestMethod.GET, params = {
    "name","age!=110"
}, headers = {
    "Host=localHost:8080"
})
public String mapping() {
    System.out.println("mapping()");
    return "success";
}
```

---

> 获取值得方式: 支持联动属性 > `address` `.` `province`

```java
@RequestMapping("setStudent")
public String setStudent(Student student) {
    System.out.println("setStudent(Student " + student + ")");
    return SUCCESS;
}
```

> 编写 HTML

```html
<form action="setStudent" method="post">
    <input type="text" name="id" />
    <input type="text" name="name" />
    <input type="text" name="gender" />
    <input type="text" name="age" />
    <input type="submit" />
</form>
```

---

> #### @PathVariable

```java
public @interface PathVariable {

	/**
	 * 获取 URL 上的值
	 * The URI template variable to bind to. : 绑定到的URI模板变量
	 */
	String value() default "";

}
```

* 使用方式

```java
@RequestMapping(value = "methodGET/{id}", method = RequestMethod.GET)
public String methodGET(@PathVariable("id") Integer id) {
    System.out.println("methodGET(" + id + ")");
    return SUCCESS;
}
```

> #### @RequestParam

```java
public @interface RequestParam {

	/**
	 * 要绑定到的请求参数的名称
	 */
	String value() default "";

	/**
	 * 是否是必须的参数
     */
	boolean required() default true;

	/**
	 * 请求参数的默认值
	 */
	String defaultValue() default ValueConstants.DEFAULT_NONE;

}
```

* 使用方式

```java
@RequestMapping(value = "getParam")
public String getParam(
    @RequestParam(value = "userName", defaultValue = "admin") String userName,
    @RequestParam(value = "password", required = false) String password) {
    
    System.out.println("getParam(String " + userName + ", String " + password + ")");
    
    return "success";
}
```

> #### @RequestHeader

```java
public @interface RequestHeader {

	/**
	 * 要绑定到的请求头的名称
	 */
	String value() default "";

	/**
	 * 是否是必须的参数
	 */
	boolean required() default true;

	/**
	 * 请求头的默认值
	 */
	String defaultValue() default ValueConstants.DEFAULT_NONE;

}
```

* 使用方式

```java
@RequestMapping("getHeader")
public String getHeader (@RequestHeader("Host") String host) {
    System.out.println("getHeader(String " + host + ")");
    return "success";
}
```

> #### @CookieValue

```java
public @interface CookieValue {

	/**
	 * 要绑定到的 Cookie key
	 */
	String value() default "";

	/**
	 * 是否是必须的参数
	 */
	boolean required() default true;

	/**
	 * 请求头的默认值
	 */
	String defaultValue() default ValueConstants.DEFAULT_NONE;

}
```

* 使用方式

```java
@RequestMapping("getCookieValue")
public String getCookieValue (@CookieValue("JSESSIONID") String JSESSIONID) {
    System.out.println("[getCookieValue(String " + JSESSIONID + ")");
    return "success";
}
```

