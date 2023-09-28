> profile

> 多Profile文件
>
> 我们在主配置文件编写的时候，文件名可以是   application-{profile}.properties/yml
>
> 默认使用application.properties的配置；

* https://cloud.spring.io/spring-cloud-static/Greenwich.SR1/single/spring-cloud.html
* find ( if you need your )
* 记好笔记一个极小的细节: yaml 和 properties 的区别还有
* yaml 在一些极端环境下可以表达加载的顺序 properties 做不到


>  2、yml支持多文档块方式

```yaml
server:
  port: 8081
spring:
  profiles:
    active: prod

---

server:
  port: 8083
spring:
  profiles: dev

---

server:
  port: 8084
spring:
  profiles: prod  #指定属于哪个环境
```

```yaml
# 文档块

---

```

> 3、激活指定profile

1、在配置文件中指定  spring.profiles.active=dev

2、命令行：

​	java -jar spring-boot-02-config-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev；

​	可以直接在测试的时候，配置传入命令行参数

3、虚拟机参数；

​	-Dspring.profiles.active=dev

---

>  6、配置文件加载位置

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

```java
–file:./config/

–file:./

–classpath:/config/

–classpath:/
```

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

SpringBoot会从这四个位置全部加载主配置文件；互补配置；

==我们还可以通过spring.config.location来改变默认的配置文件位置==

项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；指定配置文件和默认加载的这些配置文件共同起作用形成互补配置；

java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --spring.config.location=G:/application.properties

---

>  7、外部配置加载顺序

==SpringBoot也可以从以下位置加载配置； 优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置==

1.命令行参数

​	所有的配置都可以在命令行上进行指定

​	java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8087  --server.context-path=/abc

​	多个配置用空格分开； --配置项=值

2.来自java:comp/env的JNDI属性

3.Java系统属性（System.getProperties()）

4.操作系统环境变量

5.RandomValuePropertySource配置的random.*属性值

​	==由jar包外向jar包内进行寻找；==

​	==优先加载带profile==

6.jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件

7.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件

​	==再来加载不带profile==

8.jar包外部的application.properties或application.yml(不带spring.profile)配置文件

9.jar包内部的application.properties或application.yml(不带spring.profile)配置文件

10.@Configuration注解类上的@PropertySource

11.通过SpringApplication.setDefaultProperties指定的默认属性

所有支持的配置加载来源；

[参考官方文档](https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#boot-features-external-config)

----

>  #### 自动配置原理：

1）、SpringBoot启动的时候加载主配置类，开启了自动配置功能 ==@EnableAutoConfiguration==

2）、@EnableAutoConfiguration 作用：

- 利用EnableAutoConfigurationImportSelector给容器中导入一些组件？

- 可以查看selectImports()方法的内容；

- List<String> configurations = getCandidateConfigurations(annotationMetadata,      attributes);获取候选的配置

SpringFactoriesLoader.loadFactoryNames()

扫描所有jar包类路径下  META-INF/spring.factories

把扫描到的这些文件的内容包装成properties对象

从properties中获取到EnableAutoConfiguration.class类（类名）对应的值，然后把他们添加在容器中

==将 类路径下  META-INF/spring.factories 里面配置的所有EnableAutoConfiguration的值加入到了容器中；==

>  以HttpEncodingAutoConfiguration（Http编码自动配置）为例解释自动配置原理；

```java
@Configuration/** 表示这是一个配置类, 也可以给容器中添加组件 */
/** 启动指定类的 ConfigurationProperties 功能, 将配置文件中的值和HttpProperties绑定起来 */
@EnableConfigurationProperties({HttpProperties.class})
/** Spring底层 @Conditional 注解, 根据不同的条件判断是否是一个 web 工程, 如果满足配置才会生效 */
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
/** 判断当前项目有没有这个类 : CharacterEncodingFilter */
@ConditionalOnClass({CharacterEncodingFilter.class})
/** 判断配置文件中是否存在莫个配置:spring.http.encoding.enabled
	默认是: spring.http.encoding.enabled=true (matchIfMissing = true) 生效的
*/
@ConditionalOnProperty(
    prefix = "spring.http.encoding",
    value = {"enabled"},
    matchIfMissing = true
)
public class HttpEncodingAutoConfiguration {
    
    /** 已经和配置文件映射了的集合 */
    private final Encoding properties;
    
    /** 只有一个构造器的情况下参数的值就会从容器中拿 */
    public HttpEncodingAutoConfiguration(HttpProperties properties) {
        this.properties = properties.getEncoding();
    }
  
    @Bean /** 给容器中添加一个组件 */
    @ConditionalOnMissingBean /** 判断容器中是否存在该组件 */
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(org.springframework.boot.autoconfigure.http.HttpProperties.Encoding.Type.RESPONSE));
        return filter;
    }
```

> 也就是说要绑定那些属性可以通过: `前缀:spring.http` `.` `Encoding.charset`

```java
@ConfigurationProperties(prefix = "spring.http")
public class HttpProperties {
    
	public static class Encoding {

		public static final Charset DEFAULT_CHARSET = StandardCharsets.UTF_8;

		private Charset charset = DEFAULT_CHARSET;
```

---

精髓：

​	**1）、SpringBoot启动会加载大量的自动配置类**

​	**2）、我们看我们需要的功能有没有SpringBoot默认写好的自动配置类；**

​	**3）、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）**

​	**4）、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值；**

xxxxAutoConfigurartion：自动配置类；

给容器中添加组件

xxxxProperties:封装配置文件中相关属性；

2、细节

1、@Conditional派生注解（Spring注解版原生的@Conditional作用）

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

| @Conditional - 扩展注解         | 作用 ( 判断是否满足当前条件 ) |
| ------------------------------- | ----------------------------- |
| @ConditionalOnJava              | 系统的java版本是否符合要求    |
| @ConditionalOnBean              | 容器中是否存在指定bean        |
| @ConditionalMissingBean         | 容器中不存在指定bean          |
| @ConditionalOnNotWebApplication | 当前项目不是web环境           |
| @ConditionalOnWebApplication    | 当前项目是web环境             |

自动配置类必须在一定的条件下才能生效；

我们怎么知道哪些自动配置类生效；

==我们可以通过启用  debug=true属性；来让控制台打印自动配置报告==，这样我们就可以很方便的知道哪些自动配置类生效；

```java
=====
AUTO-CONFIGURATION REPORT
=====
Positive matches:（自动配置类启用的）
-----------------
   DispatcherServletAutoConfiguration matched:

      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet'; @ConditionalOnMissingClass did not find unwanted class (OnClassCondition)
      - @ConditionalOnWebApplication (required) found StandardServletEnvironment (OnWebApplicationCondition)
          
Negative matches:（没有启动，没有匹配成功的自动配置类）
-----------------
   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'javax.jms.ConnectionFactory', 'org.apache.activemq.ActiveMQConnectionFactory' (OnClassCondition)
   AopAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'org.aspectj.lang.annotation.Aspect', 'org.aspectj.lang.reflect.Advice' (OnClassCondition)
```

