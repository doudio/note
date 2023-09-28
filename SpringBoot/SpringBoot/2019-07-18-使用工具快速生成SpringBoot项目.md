> #### 使用工具快速生成 SpringBoot 项目

>  默认生成的Spring Boot项目；

- 主程序已经生成好了，我们只需要我们自己的逻辑
- `resources`: 文件夹中目录结构
- `static`：保存所有的静态资源； js css  images；
- `templates`：保存所有的模板页面；
  - （Spring Boot默认jar包使用嵌入式的Tomcat，默认不支持JSP页面）；可以使用模板引擎（freemarker、thymeleaf）；
- `application.properties`：Spring Boot应用的配置文件；可以修改一些默认设置；
  - 比如配置: 

```xml
server.port=666
```

- `application.yml`默认没有生成与`application.properties`作用差不多
  - 比如配置:

```yaml
server:
  port: 8081
```

> 编写 Controller : @RestController 适用于 REST 风格的 API

```java
@RestController/** == @Controller, @ResponseBody */
public class HelloWorldController {

    @RequestMapping("hello")
    public String hello() {
        return "RestController";
    }

}
```

> #### yaml 的基本语法

`k` `:` ` ` `v`：表示一对键值对（**空格必须有**）；

	以空格的缩进来控制层级关系；只要是左对齐的一列数据，都是同一个层级的

```yaml
server:
    port: 8081
    path: /hello
```

	属性和值也是大小写敏感；

2、值的写法

字面量：普通的值（数字，字符串，布尔）

	k: v：字面直接来写；
	
		字符串默认不用加上单引号或者双引号；
	
		""：双引号；不会转义字符串里面的特殊字符；特殊字符会作为本身想表示的意思
	
				name:   "zhangsan \n lisi"：输出；zhangsan 换行  lisi
	
		''：单引号；会转义特殊字符，特殊字符最终只是一个普通的字符串数据
	
				name:   ‘zhangsan \n lisi’：输出；zhangsan \n  lisi

>  对象、Map（属性和值）（键值对）：

	k: v：在下一行来写对象的属性和值的关系；注意缩进
	
		对象还是k: v的方式

```yaml
friends:
	lastName: zhangsan
    age: 20
```

>  行内写法：

```yaml
friends: {lastName: zhangsan,age: 18}
```

> 数组（List、Set）：

>  用- 值表示数组中的一个元素

```yaml
pets:
 - cat
 - dog
 - pig
```

>  行内写法

```yaml
pets: [cat,dog,pig]
```

> #### 使用 yaml 读取数据

> pom.xml 中配置

```xml
<!-- 导入配置文件处理器, 在编写 yaml 时就有提示了 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

> javaBean 类

```java
/**
 * @ConfigurationProperties() : 告诉StringBoot将本类的数据与配置文件绑定
 *  prefix = "user" : 与那些属性进行映射
 *		默认从主配置文件中读取	
 *
 * @Component : 只有 Spring 容器的组件才能提供 @ConfigurationProperties 的功能
 */
@Component
@ConfigurationProperties(prefix = "user")
public class User {

    private Integer id;
    private String name;
    private Date createDate;

    private List<String> lists;

    private Map<String, Object> maps;

    private Pet pet;
    
    // setter(), getter(), toString()
    
}
```

```java
public class Pet {

    private Integer id;
    private String name;
    private String gender;
    
    // setter(), getter(), toString()
	
}
```

> yaml.xml 配置的编写

```yaml
user:
  id: 110
  name: ' userName'
  create-date: 2018/11/11
  lists: ['value1','value2','value3']
  maps: {key1: value1,key2: value2,key3: value3}
  pet:
    id: 110
    name: 'pet1'
    gender: '男'
```

> 使用随机数配置属性值 > 使用:`指定默认值`
>
> ```text
> ${random.value}, ${random.int}, ${random.long}
> ${random.int(110)}, ${random.int[110]}, ${random.uuid}
> ${对象.属性}
> ```

```properties
user.id=${random.int[110]}
user.name=${random.uuid}
user.gender=${user.sex:defaultF}
```

> 编写测试类进行注入测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootHelloAutoApplicationTests {

    @Autowired private User user;

    @Test
    public void contextLoads() {
        System.out.println(user);
    }

}
```

> @Value `VS` @ConfigurationProperties 

|                | `@ConfigurationProperties` | `@Value`     |
| -------------- | -------------------------- | ------------ |
| 功能           | 批量注入属性               | 一个个的注入 |
| 松散绑定       | `v`                        | `x`          |
| SqEL           | `X`                        | `v`          |
| JSR303数据校验 | `v`                        | `x`          |
| 复杂结构       | `v`                        | `x`          |

> `@Value` 和 `@ConfigurationProperties`的选择

> 只是业务简单的需要用一下配置文件中的值可以使用: `@value`

> 专门为 `bean` 编写了多个配置可以使用: `@ConfigurationProperties `

---

```java
@ImportResource({"classpath:spring-beans.xml"})
@SpringBootApplication
public class SpringbootHelloAutoApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootHelloAutoApplication.class, args);
    }
}
```

> @PropertySource(value = "classpath:user.properties") : 指定配置文件读取

> @ImportResource(locations = {"classpath:spring-beans.xml"}) : 指定读取 Spring 配置文件

> SpringBoot 推荐往容器中添加组件的方式

```java
/** SpringBoot 推荐使用这种方式
 * @Configuration : 指定当前类是一个配置类用来代替之前的Spring配置文件
 */
@Configuration
public class AppConfig {

    /**
     * @return 将方法的返回值添加到容器中, id 默认是方法名
     */
    @Bean()
    public Pet pet () {
        return new Pet();
    }

}
```

