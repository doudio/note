> #### SpringBoot - HelloWorld

1. 创建一个`maven`项目
2. 在`pom.xml`文件中: 声明父包依赖 并且导入`springboot`启动器
3. 编写`springboot`启动类
4. 编写`springmvc`处理请求

> #### 在`pom.xml`文件中: 声明父包依赖 并且导入`springboot`启动器

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
</parent>

<dependencies>
    <!-- SpringBoot 启动器 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

> > spring-boot-starter-parent 的父项目是
>
> ```xml
> <parent>
>    <groupId>org.springframework.boot</groupId>
>    <artifactId>spring-boot-dependencies</artifactId>
>    <version>1.5.9.RELEASE</version>
>    <relativePath>../../spring-boot-dependencies</relativePath>
> </parent>
> ```
>
> > 在这个`父项目的父项目`中声明了很多 spring 开发的依赖, 是 SpringBoot 版本仲裁中心
> >
> > **以后导入依赖默认是不需要写版本的**, {当然也有些: 没有在`dependencies`里面管理的依赖自然需要声明版本号}
>
> > `spring-boot-starter`-web: 是 SpringBoot 启动器
> >
> > ​	`spring-boot-starter` : SpringBoot 场景启动器
> >
> > ​		SpringBoot 将所有的功能场景都抽取出一个个的 starter (启动器)
> >
> > ​		根据不同项目需求模块 SpringBoot会有不同的启动器供我们使用

> #### 编写`springboot`启动类

```java
/**
 * @SpringBootApplication : 标识这是一个 SpringBoot 应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {
        /**
         *	(HelloWorldMainApplication.class : 必须要是有 @SpringBootApplication 修饰的类
         *	, args : main 方法形参)
         */
        SpringApplication.run(HelloWorldMainApplication.class, args);
    }

}
```

> SpringBootApplication : 注解
>
> ```java
> @Target({ElementType.TYPE})
> @Retention(RetentionPolicy.RUNTIME)
> @Documented
> @Inherited
> @SpringBootConfiguration
> @EnableAutoConfiguration
> @ComponentScan(
>     excludeFilters = {@Filter(
>     type = FilterType.CUSTOM,
>     classes = {TypeExcludeFilter.class}
> ), @Filter(
>     type = FilterType.CUSTOM,
>     classes = {AutoConfigurationExcludeFilter.class}
> )}
> )
> public @interface SpringBootApplication
> ```
>
> > @SpringBootConfiguration : SpringBoot 的配置类, 标记在莫个类上标识这是一个 SpringBoot 的配置类
> >
> > ​	@Configuration : 配置类上标识的 Spring 配置注解
> >
> > ​		@Component : 配置类也是容器中的一个组件
>
> > @EnableAutoConfiguration : 开启自动配置
>
> ```java
> @AutoConfigurationPackage
> @Import({EnableAutoConfigurationImportSelector.class})
> public @interface EnableAutoConfiguration
> ```
>
> > 主要体现在: **(new AutoConfigurationPackages.PackageImport(metadata)).getPackageName()**
>
> ```java
> static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {
>     
>     Registrar() {
>     }
> 
>     public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
>         AutoConfigurationPackages.register(registry, (new AutoConfigurationPackages.PackageImport(metadata)).getPackageName());
>     }
> ```
>
> > @AutoConfigurationPackage : 自动配置包
>
> ```java
> @Import({Registrar.class})
> public @interface AutoConfigurationPackage
> ```
>
> > 将主配置类所在的包以及下面的所有组件扫描到 Spring 容器中
>
> > @Import({EnableAutoConfigurationImportSelector.class}) : 给容器导入那些组件
> >
> > ​	EnableAutoConfigurationImportSelector.class : 导入组件的选择器
> >
> > 源代码体现在: `org.springframework.boot.autoconfigure.AutoConfigurationImportSelector#selectImports`
> >
> > **SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class,classLoader)；**
> >
> > Spring Boot在启动的时候从类路径下的 `META-INF/spring.factories` 中获取`EnableAutoConfiguration`指定的值，将这些值作为自动配置类导入到容器中，
> >
> > 自动配置类就生效，帮我们进行自动配置工作；
> >
> > 以前我们需要自己配置的东西，自动配置类都帮我们；
> >
> > J2EE的整体整合解决方案和自动配置都在spring-boot-autoconfigure-1.5.9.RELEASE.jar；

