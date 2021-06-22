> ### SpringBoot Tomcat

> 在项目中将 `jar` 工程改成 `war` 
>
> ```xml
> <packaging>war</packaging>
> ```

> 将`tomcat`从父项目中排除
>
> ```xml
> <!-- 2. 移出 tomcat 容器 -->
> <dependency>
>     <groupId>org.springframework.boot</groupId>
>     <artifactId>spring-boot-starter-tomcat</artifactId>
>     <scope>compile</scope>
> </dependency>
> ```

> 定义 `service` 启动类
>
> ```java
> public class ServiceBoot extends SpringBootServletInitializer {
> 
> 	@Override
> 	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
> 		return builder.sources(Application.class);
> 	}
> 
> }
> ```
>
> > * `Application.class`是`SpringBoot`原程序启动类

> 在pom文件中配置启动类
>
> ```xml
> <properties>
>     <java.version>1.8</java.version>
>     <!-- 4. 声明启动的类 -->
>     <start-class>com.znsd.springboot.ServiceBoot</start-class>
> </properties>
> ```

> 配置`springboot`打包插件
>
> ```xml
> <build>
>     <finalName>springboot-tomcat-demo</finalName>
>     <plugins>
>         <plugin>
>             <groupId>org.springframework.boot</groupId>
>             <artifactId>spring-boot-maven-plugin</artifactId>
>         </plugin>
>     </plugins>
> </build>
> ```

> 编译项目: `complie`

> 打包并安装项目: `install -Dmaven.test.skip=true`

> #### 注意: SpringBoot 项目打出的包名要与 SpringBoot 配置的项目名一致

> ######  使用不同SpringBoot版本，指定访问项目路径的项目名，使用的配置也不一样

| SpringBoot版本 | 配置                              |
| -------------- | --------------------------------- |
| 1.x            | server.context-path=/demo         |
| 2.x            | server.servlet.context-path=/demo |