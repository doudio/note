> #### Maven 常规操作

### 统一版本的步骤

- 在标签内使用自定义标签来统一声明版本号。

```xml
<properties>
	<spring.version>4.3.6.RELEASE</spring.version>
</properties>
```

- 在需要使用版本的位置使用${自定义标签名}来引用版本号。

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${spring.version}</version>
</dependency>
```

- peroperteis标签当中声明的标签，除了可以用于版本号控制意外，可以声明任何标签，在需要的时候使用，类似于全局变量。

---

### `Maven`部属`Web`项目

- 在`Eclipse`中可以直接运行项目，`Eclipse`会自动部属到服务器，但是一般情况下，这种方式不适合`Maven`。
- `Maven`的部属分为两种方式：
  - 手动部属：通过`package`对`mvean`项目打包成`*.war`，然后将`war`文件拷贝到`tomcat`服务器的`webapps`目录下。
  - 一键部属：通过配置，一键部属到`tomcat`。
- 一键部属步骤：
  - 添加`tomcat`的`manager`权限。
  - 在`settings.xml`添加`manager`用户。
  - 在`pom.xml`中配置`tomcat`的`maven`插件。
  - 使用`mvn tomcat7:deploy`部属网站

#### 配置tomcat权限

- 在`tomcat`目录的`conf/tomcat-users.xml`中添加权限。

```
<tomcat-users>
    <role rolename="admin-gui"/>
    <role rolename="admin-script"/>
    <role rolename="manager-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-jmx"/>
    <role rolename="manager-status"/>
    <user username="admin" password="admin" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-script,admin-gui"/>
</tomcat-users>
```

#### maven核心配置文件

- 配置maven的settings.xml文件，让maven可以访问tomcat的权限。

```
<servers>
    <!-- 配置tomcat-/manager/text 访问权限 -->
    <server>
        <id>tomcat</id>
        <username>admin</username>
        <password>admin</password>
    </server>
</servers>
```

#### 配置pom.xml

- 在pom.xml中配置tomcat在maven的插件


```xml
<build>
    <finalName>myApp</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <version>2.2</version>
            <configuration>
                <url>http://localhost:8080/manager/text</url>
                <server>tomcat</server>
                <username>admin</username>
                <password>admin</password>
                <path>/${project.build.finalName}</path>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### 部属pom.xml

- mvn clean:install

  clean是清理输出文件，install编译打包，在每次打包之前必须执行clean，才能保证发布为最新文件

- mvn tomcat7:redeploy

  第一次发布 tomcat7:deploy，再次发布 tomcat7:redeploy