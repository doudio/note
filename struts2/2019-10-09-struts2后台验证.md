> ### struts2后台验证

1. 将逻校验逻辑码写在: validate(); 方法中
   1. 将逻辑写在 validate() 方法中, 将会校验所有 业务方法
2. 将逻辑写在 validate`Function`() 方法中
   1. 将会校验: validate后面跟的方法



---

| 返回值  | 方法名                       | 描述           |
| :-----: | ---------------------------- | -------------- |
|  void   | addFieldError("key", "mgs"); | 添加字段错误   |
|  void   | addActionError("msg");       | 添加逻辑错误   |
| boolean | hasFieldErrors();            | 是否有字段错误 |
| boolean | hasActionErrors();           | 是否有逻辑错误 |

***注意*** : 

1. 在 addFieldError(key, msg); 方法中若 `key` 值与页面 s 标签的`name`值保持一致错误消息将自动显示在输入框上方

```java
public class StudentAction extends ActionSupport {

	private String username;
	private String password;
	
    public String login() {

		if (!"administrator".equals(username)) {
			addFieldError("username", "用户名错误!");
		}
		if (!("administrator".hashCode() == password.hashCode())) {
			addFieldError("password", "密码错误!");
		}

		if (hasFieldErrors()) {
			return ERROR;
		}
        
		return SUCCESS;
	}
    
	/**
	 * 将逻辑写在 validate() 方法中, 将会校验所有 业务方法
	 */
	@Override
	public void validate() {
		
		if (!"administrator".equals(username)) {
			addFieldError("username", "用户名错误!");
		}
		if (!("administrator".hashCode() == password.hashCode())) {
			addFieldError("password", "密码错误!");
		}
		
	}
	
	/**
	 * 将逻辑写在 validateFunction() 方法中, 将会校验: validate后面跟的方法
	 */
	public void validateLogin() {
		
		if (!"administrator".equals(username)) {
			addFieldError("username", "用户名错误!");
		}
		if (!("administrator".hashCode() == password.hashCode())) {
			addFieldError("password", "密码错误!");
		}
		
	}

    Set(), Get();
    
}
```

> 页面输出错误信息

```html
<%@ taglib prefix="s" uri="/struts-tags" %>
<s:fielderror /><!-- 输出字段错误 -->

<s:fielderror><!-- 指定输出某个字段错误 -->
    <s:param>username</s:param>
</s:fielderror>

<s:actionerror /><!-- 输出逻辑错误 -->
```

**小经验**  

1. **接收参数时, 数据转换失败也会调用validate()方法。**
2. **validate()方法验证不通过，不会执行业务方法。**
3. **validate()方法和validateXxx()方法同时存在时都会起作用**
4. **validateXxx()方法的调用优先于validate()方法**



> ### **使用验证框架步骤**(xml 方式验证)

**使用验证框架步骤**   

1. 编写JSP页面
2. 编写Action及其配置文件
3. 在Action类同目录下创建`ActionName` `-validation.xml`文件
   1. **ActionName** 必须与**Action类**的名字一致
   2. **-validation.xml** 属于固定写法
   3. 也可以使用别名方式: `ActionName-`  `alias` `-validation.xm`
      1. ActionName : action类的名字
      2. alias 请求名字
      3. `-validation.xm`固定写死
4. 编写验证规则



**验证顺序**

1. Action类-validation.xml
2. Action类-alias-validation.xml



> ### struts2的几个常用的验证类

| 验证规则        | 规则说明                                                     |
| --------------- | ------------------------------------------------------------ |
| required        | 字段不能为空                                                 |
| requiredstring  | 字符串不能为空                                               |
| int             | int类型(可指定范围)                                          |
| long            | long类型(可指定范围)                                         |
| short           | short类型(可指定范围)                                        |
| double          | double类型(可指定范围)                                       |
| date            | 时间格式(可指定范围)                                         |
| expression      | ognl表达式判断                                               |
| fieldexpression | ognl表达式判断                                               |
| email           | 邮箱判断                                                     |
| url             | url路径判断                                                  |
| visitor         | 把同一个验证程序配置文件用于多个动作(对一个Bean写验证文件,每个使用的Action只要引用) |
| conversion      | 格式转换                                                     |
| stringlength    | 字符串长度                                                   |
| regex           | 正则表达式判断                                               |

```xml
<!DOCTYPE validators PUBLIC "-//Apache Struts//XWork Validator 1.0.3//EN"
		"http://struts.apache.org/dtds/xwork-validator-1.0.3.dtd">
<validators>
  <field name="bar">
      <field-validator type="required">
          <message>You must enter a value for bar.</message>
      </field-validator>
      <field-validator type="int">
          <param name="min">6</param>
          <param name="max">10</param>
          <message>bar must be between ${min} and ${max}, current value is ${bar}.</message>
      </field-validator>
  </field>
  <field name="bar2">
      <field-validator type="regex">
          <param name="expression">[0-9],[0-9]</param>
          <message>The value of bar2 must be in the format "x, y", where x and y are between 0 and 9</message>
     </field-validator>
  </field>
  <field name="date">
      <field-validator type="date">
          <param name="min">12/22/2002</param>
          <param name="max">12/25/2002</param>
          <message>The date must be between 12-22-2002 and 12-25-2002.</message>
      </field-validator>
  </field>
  <field name="foo">
      <field-validator type="int">
          <param name="min">0</param>
          <param name="max">100</param>
          <message key="foo.range">Could not find foo.range!</message>
      </field-validator>
  </field>
  <validator type="expression">
      <param name="expression">foo lt bar </param>
      <message>Foo must be greater than Bar. Foo = ${foo}, Bar = ${bar}.</message>
  </validator>
</validators>
```

---

> **使用ActionSupper实现验证有三种方式**

1. 在业务方法里面实现数据校验
2. 在validate()方法中实现数据校验
3. 在validateXxx()方法中实现数据校验

> **Action类中添加错误信息有两种方式**

1. 使用addFieldError() 添加字段错误信息
2. 使用addActionError() 方法添加Action业务相关的错误信息

> **使用验证框架验证**

1. ActionName-validation.xml
2. ActionName-alias-validation.xml
3. 验证框架规则看考帮助文档