>  ### 表单的重复提交问题

1. 在 `<s:form>` 标签下定义`<s:token>`标签
   1. 该标签会随机生成一串代码, 并保存到 Session 中
   2. 在表单提交时判断该代码是否是之前放入的代码从此达到禁止表单提交的效果
2. 在配置 Action 时指定 token 拦截器 : ` [ token | tokenSession ]`
   1. token 拦截器会在表单重复提交后找: result name 为: `invalid.token`的逻辑页面
   2. tokenSession 在表单重复提交后, 则不会执行后续 ( 拦截器 ) 操作
   3. 在配置拦截器时建议将 token 或 tokenSession 拦截器定义在最前面, 在判断表单不是重复替换后再执行后续操作



> ### 在 `<s:form>` 标签下定义`<s:token>`标签

```jsp
<s:form action="token.action" method="post">
    <s:token></s:token>
    <s:textfield name="username" label="用户名"></s:textfield>

    <s:submit></s:submit>
</s:form>
```

> 在配置 Action 时指定 token 拦截器 : ` [ token | tokenSession ]`

*配置 tokenSession 拦截器*

```xml
<action name="token" class="com.znsd.struts.action.TokenAction">
    <interceptor-ref name="tokenSession"></interceptor-ref>
    <interceptor-ref name="defaultStack"></interceptor-ref>

    <result>/token-success.jsp</result>
</action>
```

*配置 token 拦截器*

```xml
<action name="token" class="com.znsd.struts.action.TokenAction">
    <interceptor-ref name="token"></interceptor-ref>
    <interceptor-ref name="defaultStack"></interceptor-ref>

    <result>/token-success.jsp</result>
    <result name="invalid.token">/token-error.jsp</result>
</action>
```

*com.znsd.struts.action.TokenAction : 类的实现*

```java
public class TokenAction extends ActionSupport {
    
	private String username;

	@Override
	public String execute() throws Exception {
		System.out.println("用户名: " + username);
		return SUCCESS;
	}
	
    Get(), Set();
    
}
```

---

---

> ### 尚硅谷笔记

1). 什么是表单的重复提交

	> 在不刷新表单页面的前提下: 
		>> 多次点击提交按钮
		>> 已经提交成功, 按 "回退" 之后, 再点击 "提交按钮".
		>> 在控制器响应页面的形式为转发情况下，若已经提交成功, 然后点击 "刷新(F5)"
		
	> 注意:
		>> 若刷新表单页面, 再提交表单不算重复提交
		>> 若使用的是 redirect 的响应类型, 已经提交成功后, 再点击 "刷新", 不是表单的重复提交

2). 表单重复提交的危害:  			

3). Struts2 解决表单的重复提交问题:

I. 在 s:form 中添加 s:token 子标签

```html
<s:form action="login.action" method="post">
    <s:token></s:token>
</s:form>
```

	> 生成一个隐藏域
	> 在 session 添加一个属性值
	> 隐藏域的值和 session 的属性值是一致的. 

II. 使用 Token 或 TokenSession 拦截器. 

	> 这两个拦截器均不在默认的拦截器栈中, 所以需要手工配置一下
	> 若使用 Token 拦截器, 则需要配置一个 token.valid 的 result
	> 若使用 TokenSession 拦截器, 则不需要配置任何其它的 result

```xml
<action name="login" class="com.znsd.struts.action.UserAction" method="login">
    <interceptor-ref name="tokenSession"></interceptor-ref>
    <interceptor-ref name="defaultStack"></interceptor-ref>

    <result>/success.jsp</result>
    <result name="invalid.token">/error.jsp</result>
</action>
```

III. Token VS TokenSession

	> 都是解决表单重复提交问题的
	> 使用 token 拦截器会转到 token.valid 这个 result
	> 使用 tokenSession 拦截器则还会响应那个目标页面, 但不会执行 tokenSession 的后续拦截器. 就像什么都没发生过一样!

IV. 可以使用 s:actionerror 标签来显示重复提交的错误消息. 
该错误消息可以在国际化资源文件中覆盖. 该消息可以在 struts-messages.properties 文件中找到

struts.messages.invalid.token=^^The form has already been processed or no token was supplied, please try again.