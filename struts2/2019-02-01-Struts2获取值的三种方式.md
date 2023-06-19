> ## Struts2获取值的三种方式

1. 通过属性 ( attr ) 获取值
2. 通过对象 ( object ) 获取值
3. 通过模型 ( model ) 获取值



> ### 通过属性 ( attr ) 获取值 [ 少量数据推荐使用 ]

1. 在 Action 类中定义属性
2. 生成 Set(); Get(); 方法

```java
public class GetParamAttrAction implements Action {
	private String name;
	private Integer age;
	private Date bir;
	
    Set(); Get(); toString();
    
    @Override
    public String execute() throws Exception {
		System.out.println("execute : " + this);
		return SUCCESS;
	}
}
```

```html
页面赋值: <input type="text" name="name" />
```

```html
jsp获取: 姓名: ${name }
```



> ### 通过对象 ( object ) 获取值

1. 定义标准的 javaBean
2. 在 Action 类中声明, 并生成 Set(); Get(); 方法

```java
public class GetParamObjectAction implements Action {
	private Student student;
	
    Set(); Get(); toString();
    
    @Override
	public String execute() throws Exception {
		System.out.println(student);
		return SUCCESS;
	}
}
```

```html
页面赋值: <input type="text" name="student.name" />
```

```html
jsp获取: 姓名: ${student.name }
```



> ### 通过模型 ( model ) 获取值

1. 实现 ModelDriven 接口, 接口泛型为实体类 类型
2. 在类中定义 实体对象, 并生成 Set(); Gey(); 方法
3. 重写接口中的 getModel() : 方法
   1. 重写方式将 定义好的实体对象返回

```java
public class GetParamModel implements ModelDriven<Student>, Action {
	private Student student = new Student();
	
    Set(); Get(); toString();
    
    @Override
	public Student getModel() {
		return student;
	}
    
	@Override
    public String execute() throws Exception {
		System.out.println("student: " + student);
		return SUCCESS;
	}
}
```

```html
页面赋值: <input type="text" name="name" />
```

```html
jsp获取: 姓名: ${student.name }
```

#### 通过对象 ( object ) 获取值 与 通过模型 ( model ) 获取值 的区别

1. 相同点：他们都可以将数据封装到实体类对象当中去  
2. 不同点：模型驱动封装只能把数据封装到一个实体类对象当中去，因为getModel()方法只会返回一个实体类的对象；而表达式封装则可以把数据封装到不同的实体类对象里面。 



> ## 特殊情况的处理

1. 将值封装到 List 集合中
2. 将值封装到 Set 集合中
3. 将值封装到 Map 集合中
4. 由于要将元素下标写死, 没有应用场景不跟新了