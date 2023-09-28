``` xml
<!-- 
	namespace : 忘记加 [ / ] 
		导致 页面(404) There is no Action mapped for action name redirectActionTest
-->
<package name="struts-test" extends="struts-default"
	namespace="/dir">
	<action name="redirectActionTest">
		<result>/redirectAction.jsp</result>
	</action>
</package>
```

```java
/* 遇见过的异常 */
java.lang.IllegalArgumentException: Can not find a java.io.InputStream with the name [INputStream] in the invocation stack. Check the <param name="inputName"> tag specified for this action.
该异常表示, 你声明的 getInputStream 与 配置的 <param name="inputName">inputStream</param> 不匹配
```

