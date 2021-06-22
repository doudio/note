> #### znsd - springboot - helloworld

- 如若要返回`jsp`页面，则需要添加依赖

```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
    <scope>provided</scope>
</dependency>
```

- 可在`springboot`的核心配置文件中配置`springMVC`的视图解析

```properties
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```

##### **开发环境的调试**

- 热启动：更改了内容自动重新加载启动服务器
- pom中添加模块

```xml
<!-- 热启动配置 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
            </configuration>
        </plugin>
    </plugins>
</build>
```

> SpringBoot将项目打出`jar`包

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>1.4.2.RELEASE</version>
        </plugin>
    </plugins>

    <resources>
        <!-- 打包时将jsp文件拷贝到META-INF目录下 -->
        <resource>
            <!-- 指定resources插件处理哪个目录下的资源文件 -->
            <directory>src/main/webapp</directory>
            <!--注意此次必须要放在此目录下才能被访问到 -->
            <targetPath>META-INF/resources</targetPath>
            <includes>
                <include>**/**</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/**</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

---
