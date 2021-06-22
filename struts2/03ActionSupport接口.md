> ## ActionSupport类

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 为了让用户开发的Action类更加规范，Struts2提供了一个Action接口，这个接口定义了Struts2的Action处理类应该实现的规范。下面是标准Action接口的代码： 

``` java
public interface Action {

    //定义Action接口里包含的一些结果字符串
    public static final String SUCCESS = "success";
    public static final String NONE = "none";
    public static final String ERROR = "error";
    public static final String INPUT = "input";
    public static final String LOGIN = "login";

    //定义处理用户请求的execute()方法
    public String execute() throws Exception;
}
```

1. success ： 表示请求处理成功，该值也是默认值。  
2. error ：表示请求处理失败。  
3. none ：表示请求处理完成后不跳转到任何页面。  
4. input ：表示输入时如果验证失败应该跳转到什么地方。  
5. login ：表示登录失败后跳转的目标。 



> ## ActionSupport类就是实现了这个接口

```java
public class ActionSupport 
    implements Action, Validateable, ValidationAware, TextProvider, LocaleProvider, Serializable 
```

1. Validateable : 手工实现验证
2. ValidationAware : 错误消息, 错误消息有: 字段级别的, action 级别的
3. TextProvider AND LocaleProvider : 在作程序国际化时能用到
4. 在手工完成字段验证, 显示错误消息, 国际化等情况下, 推荐继承 ActionSupport



> ## Struts Result Type ( 一共十种类型  )

| name                      | describe                                                     |
| :------------------------ | :----------------------------------------------------------- |
| dispatcher                | 默认的类型 相当于 Servlet 的  foward 用户 URL 不变           |
| redirect、redirect-action | 页面重定向，客户端跳转；前者用于跳转到jsp页面，后者用于跳转到action |
| chain                     | 将请求转发到一个action                                       |
| stream                    | 一般用于下载文件用                                           |
| PlainText                 | 普通文本                                                     |
| Velocity(Velocity)        | 用于与Velocity技术的集成                                     |
| XSLT(xslt)                | 用于与XML/XSLT技术的集成                                     |
| HttpHeader                | 返回报文头给用户                                             |
| json                      | 返回一个 json 文本                           |							|

注意: 

1. redirect 可以实现 redirectAction 的功能
2. 而 dispatcher 不能实现 chain 的功能



> ## Struts2 通配符匹配

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个 Web 应用可能有成百上千个 action 声明. 可以利用 struts 提供的通配符映射机制把多个彼此相似的映射关系简化为一个映射关系

通配符映射规则:

1. 使用通配符匹配, 若有精确匹配精确匹配 优先

  . 若 没有 精确匹配, Struts 将会尝试把这个 URI 与任何一个包含着通配符 * 的动作名及进行匹配	

2. 被通配符匹配到的 URI 字符串的子串可以用 {1}, {2} 来引用. {1} 匹配第一个子串, {2} 匹配第二个子串…

3. {0} 匹配整个 URI

4. 若 Struts 找到的带有通配符的匹配不止一个, 则按先后顺序进行匹配

   ​	( **若遇见两次模糊匹配, 则考虑代码上下文优先级, 找到了就不找了** )

5. **可以匹配零个或多个字符, 但不包括 / 字符. 如果想把 / 字符包括在内, 需要使用 **. 如果需要对某个字符进行转义, 需要使用 \.

```xml
<action name="student-*" class="com.znsd.struts2.action.Student" method="{1}">
    <result name="{1}-success">/success.jsp</result>
</action>
```



> ## 动态方法调用 ( 了解 )

```xml
<!-- 开启动态方法调用, 默认为 false -->
<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>

<!-- 注册一个 Action 类, 类中有两个方法: dyTest(), dyDemo() -->
<action name="testDynamicMethod" 
        class="com.znsd.struts2.action.TestDynamicMethodInvocation"
        method="dyTest">
    <result>/success.jsp</result>
</action>
```

> 在URL中调用动态方法: 
>
> ​	http://localhost:8080/2018-8-10-struts2-05-type/testDynamicMethod!`dyDemo`.action
>
> dyDemo	: 方法名, 这里输入什么方法就会调用什么方法, 不好的的方, 调用那个方法给外界看到了 !

```xml
<!-- 2.5 版本后需要配置此属性才能够使用动态方法和通配符 -->
<global-allowed-methods>regex:.*</global-allowed-methods>
<action name="userinfoAction" class="com.znsd.struts2.action.Student">
    <result>/index.jsp</result>
</action>
```
`注意: ` 2.5 版本后为了安全动态方法和通配符在使用的时候需要添加如下代码：  
```xml
<global-allowed-methods>regex:.*</global-allowed-methods>
```
> ### 动态返回结果

```java
public class DyResult extends ActionSupport {
	private String doJsp;	// 定义动态返回结果
	public String getDoJsp() {
		return doJsp;
	}

	@Override
	public String execute() throws Exception {
		return SUCCESS;
	}
}
```

```xml
<!-- 
	<result name="${returnString}">${jsp}</result>
         ${returnString}: 不能写成动态的
         ${jsp}: 可以动态转到某个页面
-->
<action name="DyResult" class="com.znsd.struts2.action.DyResult">
    <!-- 获取定义的动态返回结果 -->
    <result>${jsp}</result>
</action>
```

> ### 配置全局参数

```xml
<package name="default" extends="struts-default" namespace="/">
    <!-- 若我输入的 Action 没有找到, 就默认一个 Action 给他 -->
    <default-action-ref name="resultTest"></default-action-ref>

    <!-- 若 Action 没有对于的 result 再来找一次 global-results -->
    <global-results>
        <result name="default-result">/temp.jsp</result>
    </global-results>
    
    <action name="resultTest" class="com.znsd.struts2.action.ResultTest"></action>
    <action name="dyResult" class="com.znsd.struts2.action.DyResult"></action>
</package>
```

