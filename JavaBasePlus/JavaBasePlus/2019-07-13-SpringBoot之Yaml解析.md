> #### SpringBoot 之 yaml 解析

1. 导入 `SpringBoot` 的 `snakeyaml` 解析包
2. 编写 `yaml` 文件
3. 编写 `yaml` 文件对应的 `class`
4. 编写 `main` 进行获取

> 导入 `SpringBoot` 的 `snakeyaml` 解析包

```xml
<dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>1.23</version>
</dependency>
```

> 编写 `yaml` 文件

```yaml
id: 110
name: 'stuName'
gender: '男'
clas:
  id: 110
  name: 'classesName'
  describe: 'describe'
```

> 编写 `yaml` 文件对应的 `class`

```java
public class Student {

	private Integer id;
	private String name;
	private String gender;

	private Classes clas;

	// getter(), setter(), toString();

}

class Classes {

	private Integer id;
	private String name;
	private String describe;

	// getter(), setter(), toString();

}
```

> 编写 `main` 进行获取

```java
public static void main(String[] args) {

	Yaml yaml = new Yaml();

	Student result = yaml.loadAs(thisClass.class.getClassLoader()
                                 .getResourceAsStream("springboot.yaml"),
                                 Student.class);

	System.out.println(result);

}
```

