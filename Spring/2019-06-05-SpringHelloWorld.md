> ### Spring Hello World

1. 导入 Spring 所需`jar`包
   1. spring-context-x.x.x.RELEASE.jar
   2. spring-aop-x.x.x.RELEASE.jar
   3. spring-beans-x.x.x.RELEASE.jar
   4. spring-core-x.x.x.RELEASE.jar
   5. commons-logging-x.x.jar
   6. spring-expression-x.x.x.RELEASE.jar
2. 创建一个`java`类

```java
public class HelloWorld {

	private String name;

	public void setName(String name) {
		this.name = name;
	}

	public void hello() {
		System.out.println("hello : " + name);
	}

}
```

3. 在 `src` 中配置 spring 文件`beans.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
   <!-- bean就是java对象  由spring来创建和管理 -->    
   
   <!-- id是bean的标识符 要唯一  如果没有配置id，name默认标识符 
   		如果配置id，又配置了name 那么name是别名
   		name可以设置多个别名 分隔符可以 是 空格 逗号 分号
   		class是bean的全限定名=包名+类名
   		如果不配置 id,和name 那么可以根据applicationContext.getBean(Class) 获取对象
   -->
   <!-- 通过空set方法来创建 -->
   <bean name="helloWorld" class="com.znsd.spring.bean.HelloWorld">
   	<property name="name" value="sisi" />
   </bean>
   
</beans>
```

3. 编写类测试

```java
public static void main(String[] args) {
		
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
    
    HelloWorld bean = (HelloWorld)context.getBean("helloWorld");
    bean.hello();

}
```

---

> 以上方式是通过 Set 方法将 String 对象注入到 HelloWorld 对象中的
>
> IOC 对象注入方式分为一下:

1. set 方法
2. 构造方法
   1. 通过下标方式锁定值
   2. 通过参数名锁定值
   3. 通过参数类型锁定值
3. 工厂类
   1. 静态工厂
   2. 动态工厂

> ### set 方法

```java
public class HelloWorld {

	private String name;

	public void setName(String name) {
		this.name = name;
	}

	public void hello() {
		System.out.println("hello : " + name);
	}

}
```

```xml
<bean name="helloWorld" class="com.znsd.spring.bean.HelloWorld">
    <property name="name" value="sisi" />
</bean>
```

> ### 构造方法

```java
public class HelloWorld {

	private String name;

	public HelloWorld(String name) {
		this.name = name;
	}
    
	public void hello() {
		System.out.println("hello : " + name);
	}

}
```

```xml
<bean name="helloWorld" class="com.znsd.spring.bean.HelloWorld">
    <!-- 构造方法参数下标 -->
    <constructor-arg index="0" value="sisi"></constructor-arg>
    <!-- 构造方法中的常数名 -->
    <constructor-arg name="name" value="sisi"></constructor-arg>
    <!-- 构造方法中的参数类型 -->
    <constructor-arg type="java.lang.String" value="sisi"></constructor-arg>
</bean>
```

```xml
<constructor-arg index="" name="" type="" value="" ref=""></constructor-arg>
    index	: 构造方法参数下标
    name	: 构造方法中的常数名
    type	: 构造方法中的参数类型
    value	: 值, 可以是八大基本类型 AND String
    ref		: 值, beans文件中的对象
```

> ### 工厂类

1. 静态工厂类

```java
public class HelloWorld {

	private String name;

	public HelloWorld(String name) {
		this.name = name;
	}
    
	public void hello() {
		System.out.println("hello : " + name);
	}

}
```

```java
public class HelloFactory {
	public static HelloWorld helloWorld (String name) {
		return new HelloWorld(name);
	}	
}
```

```xml
<bean id="helloFactory" class="com.znsd.spring.bean.HelloFactory" factory-method="helloWorld">
    <constructor-arg name="name" value="sisi"></constructor-arg>
</bean>
```

2. 动态工厂类

```java
public class HelloFactory {
	public HelloWorld helloWorld (String name) {
		return new HelloWorld(name);
	}	
}
```

```xml
<bean id="helloFactory" class="com.znsd.spring.bean.HelloFactory"></bean>
   
<bean id="helloDyFactory" factory-bean="helloFactory" factory-method="helloWorld">
    <constructor-arg name="name" value="sisi"></constructor-arg>
</bean>
```

> ### 自动装配`注入`


```xml
<!-- autowire="constructor" : 通过构造器注入, 构造器根据类型进行判断 -->
<!-- autowire="byType" : 通过set方法注入, 根据类型进行判断 -->
<!-- autowire="byName" : 通过set方法注入, bean : id 属性进行注入 -->
<bean id="studentService" class="com.znsd.spring.bean.StudentService"></bean>
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
       default-autowire="byName">
    <!-- 配置全局自动注入 : default-autowire="byName" -->
```

---

> ### 各种注入方式`应用场景较少`

> #### 3.3 数组注入

```xml
<property name="books">
    <array>
        <value>傲慢与偏见</value>
        <value>仲夏夜之梦</value>
        <value>雾都孤儿</value>
    </array>
</property>
```


> #### 3.4 List集合注入


``` xml
<property name="hobbies">
    <list>
        <value>羽毛球</value>
        <value>乒乓球</value>
        <value>玻璃球</value>
        <value>台球球</value>
    </list>
</property>
```


> #### 3.5 Map集合注入


> Map注入的第一种方式


```xml
<property name="cards">
    <map>
        <entry key="中国银行" value="149127348932174"/>
        <entry key="建设银行" value="456715123123541"/>
    </map>
</property>
```


> Map注入的第二种方式


```xml
<property name="cards">
    <map>
        <entry>
            <key><value>建设银行</value></key>
            <value>622710023478234234</value>
        </entry>
        <entry>
            <key><value>中国银行</value></key>
            <value>149127348932174</value>
        </entry>
    </map>
</property>
```


#### 3.6 Set集合注入


``` xml
<property name="games">
    <set>
        <value>lol</value>
        <value>dota</value>
        <value>cs1.6</value>
        <value>dnf</value>
    </set>
</property>
```


#### 3.7 null 注入


``` xml
<property name="wife"><null/></property>
```


#### 3.8 Properties注入


``` xml
<property name="info">
    <props>      
        <prop key="sex">男</prop>
        <prop key="name">小明</prop>
    </props>
</property>
```


#### 3.9 p命名空间注入


	 p命名空间注入 属性依然要设置set方法


	注意：使用p命名空间需要在头文件中加入xmlns:p="http://www.springframework.org/schema/p"，才能使用


``` xml
<bean id="u1" class="com.znsd.pojo.UserPojo" p:name="张无忌"></bean>
```


#### 3.10 p命名空间注入 


	c命名空间注入要求有对应参数的构造方法


```jsp
注意：使用c命名空间需要在头文件中加入xmlns:c="http://www.springframework.org/schema/c"，才能使用
```


``` xml
<bean id="u1" class="cn.sxt.vo.User" c:name="nico" c:age="16"/>
```