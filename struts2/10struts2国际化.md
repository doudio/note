> ### struts2 - 国际化

1. 在 src 下编写以 `i18n` 开头的文件国际化文件, 后缀为`.properties`
2. 在 Struts2 配置文件中导入
3. 导入 struts2 标签库, 使用 `.properties` 文件中的 `key` 



> ### 编写 .properties 文件

1. 命名规范: `i18n_语言_国家.properties` 

```
i18n_zh_CN.properties
i18n_en_US.properties
```

> i18n_zh_CN.properties : 

```java
index.username=\u7528\u6237\u540D
index.password=\u5BC6\u7801
index.submit=\u63D0\u4EA4
index.us=\u7F8E\u56FD
index.ch=\u4E2D\u56FD
success.welcome=\u6B22\u8FCE : {0}
success.msg=\u6E29\u99A8\u63D0\u793A : ${msg}
```

1. 将中文转换为 : \u5BC6 , 可以使用jdk自带的工具: Java\jdk1.8.0_112\bin\native2ascii.exe

> i18n_en_US.properties

```java
index.username=username
index.password=password
index.submit=submit
index.us=the United States
index.ch=China
success.welcome=welcome : {0}
success.msg=prompt : ${msg}
```



> ### 在 Struts2 配置文件中导入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>

	<constant name="struts.devMode" value="true"></constant>
    <!-- value : 国际化文件的统一开头名字 -->
	<constant name="struts.custom.i18n.resources" value="i18n" />

	<package name="default" extends="struts-default">

		<action name="login" class="com.znsd.struts.action.UserAction" method="login">
			<result>/success.jsp</result>
		</action>

		<action name="shift" class="com.znsd.struts.action.UserAction" method="shift">
			<result>/index.jsp</result>
		</action>

	</package>

</struts>
```

> 编写处理页面请求的 Action

```java
package com.znsd.struts.action;

import java.util.Locale;

import org.apache.struts2.ServletActionContext;

import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.ActionSupport;

public class UserAction extends ActionSupport {

	private String username;
	private String password;
	private String welcome;
	private String msg;

	public String login() {
        
		welcome = getText("success.welcome", new String[] { username });
         msg = "登陆成功!";
		
		return SUCCESS;
	}

	public String shift() {
		String shift = ServletActionContext.getRequest().getParameter("shift");

		Locale locale = Locale.getDefault();

		if ("us".equals(shift)) {
			locale = Locale.US; // 将语言切换成 英文
		} else {
			locale = Locale.SIMPLIFIED_CHINESE;	// 将语言切换成 中文
		}

		ActionContext.getContext().setLocale(locale);

		msg = "切换成功!";
		
		return SUCCESS;
	}
    
    Get(), Set();
    
}
```



> ### 导入 Struts2 标签使用国际化

> index.jsp

```html
<%@ taglib prefix="s" uri="/struts-tags"%>

<!-- 自定以业务逻辑实现切换 -->
<a href="shift?shift=ch">中文</a>
<a href="shift?shift=us">the United States</a>
<!-- 使用 Struts2 的拦截器实现,testI18n.action 可以是一个空请求,但是一定要经过 Struts2的拦截器 -->
<a href="testI18n.action?request_locale=en_US">英文</a>
<a href="testI18n.action?request_locale=zh_CH">中文</a>

<s:form action="login.action" method="post">
    <s:textfield name="username" key="index.username"></s:textfield>
    <s:password name="password" key="index.password"></s:password>

    <s:submit value="%{getText('index.submit')}"></s:submit>
</s:form>
```

> success.jsp

```html
<%@ taglib prefix="s" uri="/struts-tags"%>
    
${welcome }

<s:text name="success.msg" /><!-- 单独获取国际化文件中的值 -->
    
<a href="testI18n.action">to testI18n.action</a>
```

1. %{getText('index.submit')} 强制进行 OGNL 解析, 获取国际化中的值
2. `<a href="testI18n.action?request_locale=en_US">英文</a>` 
   1. 使用 Struts2 的拦截器实现,testI18n.action 可以是一个空请求,但是一定要经过 Struts2的拦截器
   2. `<a href="testI18n.action">to testI18n.action</a>` 跳转成功后点击该连接经过 Struts2 拦截器后页面就会显示之前存入 Session 中的语言
   3. 他会在点击超链接后将更改的语言放入 Session 中