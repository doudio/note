> jsp脚本提取struts2中action的属性值

```jsp
<%@page import="com.opensymphony.xwork2.ognl.OgnlValueStack"%>
<%@page import="com.bean.User"%>
<%
	OgnlValueStack stack = (OgnlValueStack) request.getAttribute("struts.valueStack");
	User user = (User)(stack.findValue("user"));
%> 
```

