> ### Struts2 actionContext 获取 作用域中的值

---

##### ActionContext 类

|       返回值        |          方法          |                  描述                  |
| :-----------------: | :--------------------: | :------------------------------------: |
|    ActionContext    |  static getContext()   | ActionContext中有获取各作用域的map方法 |
| Map<String, Object> |    getApplication()    |         获取 Appliction > map          |
| Map<String, Object> |      getSession()      |         获取 getSession > map          |
| Map<String, Object> | context.get("request") |           获取 request > map           |
| Map<String, Object> |    getParameters()     |           获取请求参数的map            |

#### 注意:

    actionContext 中并没有提供 getRequest() 来获取 request 对应的 map
    getParameters 返回的值为 Map<String, Object>
    	而不是 Map<String, String[]>, 但是他 class.getName() : 是数组类型
    parameters 这个 map 只能读, 不能写入数据, 如果写入数据不出错也不起作用!

``` java
public String execute () {

    ActionContext context = ActionContext.getContext();
		
	// 获取 Application-map
	Map<String, Object> application = context.getApplication();
	application.put("applicationKey", "applicationValue");
		
	// 获取 session-map
	Map<String, Object> session = context.getSession();
	session.put("sessionKey", "sessionValue");
		
	// 获取 request-map
	// actionContext 中并没有提供 getRequest() 来获取 request 对应的 map
	Map<String, Object> request = (Map<String, Object>) context.get("request");
	request.put("requestKey", "requestValue");
    
    // 注意在 Request中存值可以直接通过 context 来实现不需要先获取 requsetMap
    context.put("requestKey", "requestValue");
	
	// 获取请求参数的 map
	// 注意:
	// 		1. getParameters 返回的值为 Map<String, Object>, 而不是 Map<String, String[]>
	// 		2. parameters 这个 map 只能读, 不能写入数据, 如果写入数据不出错也不起作用!
	Map<String, Object> parameters = context.getParameters();
	for (Map.Entry<String, Object> entry : parameters.entrySet()) {
		System.out.println(entry.getValue().getClass() + ":" + entry.getKey());
	}
	parameters.put("paramKey", new String[] {"runExecute"});
	parameters.clear();
	
	return "success";
}
```

1. 注意在 Request中存值可以直接通过 context 来实现不需要先获取 requsetMap



> ### Struts2 获取 Servlet API 对象

---

##### 通关 ServletActionContext 类获取

|       返回值       |            方法             |
| :----------------: | :-------------------------: |
|   ServletContext   | static getServletContext(); |
| HttpServletRequest |    static getRequest();     |

```java
public void execute () {
	ServletContext servletContext = ServletActionContext.getServletContext();
	HttpServletRequest request = ServletActionContext.getRequest();
	HttpSession session = request.getSession();
	return "success";
}
```

> ### 方式二 - 通过实现接口

---

|         接口         | servlet API 对象 |
| :------------------: | :--------------: |
| ServletContextAware  |   application    |
| ServletRequestAware  |      requse      |
| ServletResponseAware |     response     |

```java
/**
 * 通过实现 ServletXxxAware 接口的方式可以由 Struts2 注入需要的 Servlet 相关对象
 *	ServletContextAware	: application
 *	ServletRequestAware	: requse
 *	ServletResponseAware: response
 */
public class TestServletAwareAction 
    implements ServletContextAware, ServletRequestAware, ServletResponseAware {

	private ServletContext context;
	private HttpServletRequest request;
	private HttpServletResponse response;

	@Override
	public void setServletContext(ServletContext context) {
		System.out.println(context);
		this.context = context;
	}

	@Override
	public void setServletRequest(HttpServletRequest request) {
		System.out.println(request);
		this.request = request;
	}

	@Override
	public void setServletResponse(HttpServletResponse response) {
		System.out.println(response);
		this.response = response;
	}
	
	public String execute() {
		return "success";
	}
}
```



> ### 通过实现接口 - 拿到非耦合的 Servlet Map 

```java
/**
 * 通过实现 XxxAware 接口的方式可以由 Struts2 注入需要的 servlet 参数 Map, 非耦合的方式
 *	RequestAware	: requse
 *	SessionAware	: session
 *	ApplicationAware	: application
 */
public class ImplMapTest extends ActionSupport 
	implements RequestAware, SessionAware, ApplicationAware {

	private Map<String, Object> request;
	private Map<String, Object> session;
	private Map<String, Object> application;

	@Override
	public String execute() throws Exception {
		return super.execute();
	}

	@Override
	public void setRequest(Map<String, Object> request) {
		this.request = request;
	}

	@Override
	public void setSession(Map<String, Object> session) {
		this.session = session;
	}

	@Override
	public void setApplication(Map<String, Object> application) {
		this.application = application;
	}

}
```

