> ### properties 文件的编写与读取

1. 编写 `.properties` 文件
   1. 文件编写以 [ `key`=`value` ] 格式
   2. `=` 符号是允许有空格的, 程序读取时会去除 `=` 左右空格
   3. 数据以换行来标识结束
2. 定义类获取`.peroperties`文件中的值
   1. 获取本类 `Class` 对象
   2. 通过 `Class` 对象获取 `ClassLoader` 类加载器
   3. 通过类加载器的 `getResourceAsStream()` 方法获取字节流
   4. 定义 `Properties` 集合, 调用集合中的 `load(inputStream)` 来读取文件
   5. 通过 `Properties` 集合中的 `getProperty("key")` 来获取对于的值

---

> 编写 `.properties` 文件

```properties
key=value
mark=markValue
info=infoVlaue
```

> 定义类获取`.peroperties`文件中的值

```java
public static void main(String[] args) throws IOException {
	InputStream inputStream = PropertiesDemo.class.getClassLoader().getResourceAsStream("info.properties");
		
	Properties properties = new Properties();
	properties.load(inputStream);
		
	System.out.println("key:[" + properties.getProperty("key") + "]");
	System.out.println("mark:[" + properties.getProperty("mark") + "]");
	System.out.println("info:[" + properties.getProperty("info") + "]");

}
```

> 那么执行程序将输出结果为: 

```html
key:[value]
mark:[markValue]
info:[infoVlaue]
```

