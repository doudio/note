> #### 自JDK1.8以后接口和抽象类的区别

> 抽象类
>
> 1. 可以定义: `protected` 属性
> 2. 抽象类中定义构造器
> 3. 抽象类中可以定义: `final` 方法

```java
public abstract class AbstractClass {
	
	protected String name;
	
	public AbstractClass(String name) {
		this.name = name;
	}
	
	protected final void method() {}
	
}
```

> 接口
>
> 1. 接口中定义的变量只能是: `public` `static` `final` 修饰
> 2. 定义普通方法

```java
interface Interface {
	
	String NAME = "";
	
	default void method() {}
	
}
```

