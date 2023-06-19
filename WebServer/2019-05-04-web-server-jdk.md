> ### web server jdk

> 编写服务端

> 服务端接口方法

```java
@WebService(targetNamespace = "WebServerInterface")
public interface WebServerInterface {

	@WebMethod
	public String hello (String hello);
	
}
```

> 接口实现方法

```java
@WebService(targetNamespace = "webServerInterfaceImpl")
public class WebServerInterfaceImpl implements WebServerInterface {

	@Override
	public String hello(String hello) {
		System.out.println("WebServerInterfaceImpl.hello()");
		return hello;
	}

}
```

> 开启服务端

```java
public class Appliction {

    /** http://localhost:8080/hello?WSDL	: 查看接口文档 */
	public static void main(String[] args) {
		Endpoint.publish("http://localhost:8080/hello", new WebServerInterfaceImpl());
		System.out.println("Appliction.main()");
	}
	
}
```

----

> 客服端 调用

> 创建项目, 在 src 目录下执行: 
>
> ​	`wsimport -p com.znsd.webservice -keep http://localhost:8080/hello?WSDL`

```cmd
F:\eclipse-workspace4\01-web-client\src>wsimport -p com.znsd.webservice -keep http://localhost:8080/hello?WSDL
正在解析 WSDL...

正在生成代码...

正在编译代码...
```

> 在生成的代码调用

```java
public static void main(String[] args) {
    WebServerInterfaceImpl impl = new WebServerInterfaceImplService().getWebServerInterfaceImplPort();

    String hello = impl.hello("Hello WebServer");

    System.out.println(hello);
}
```

