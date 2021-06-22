> ## maven 引入外部jar包

> ### 1 在`pom`文件中直接添加 `dependency`

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>sdk</artifactId>
    <version>1.0</version>
    <scope>system</scope>
    <systemPath>${basedir}/lib/sdk-1.0.jar</systemPath>
</dependency>
```

1. groupId: 公司包路径
2. artifactId: 项目名
3. version: jar包版本
4. scope: jar包的存活阶段
5. systemPath: 指明本地jar包路径: ${basedir} = 项目根目录

> scope

| scope    | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| compile  | 默认值，属于强依赖。打包之时，会达到包里去。                 |
| test     | 只参与测试相关的内容，包括测试用例的编译和执行，比如定性的Junit。 |
| runtime  | 依赖仅参与运行周期中的使用。一般这种类库都是接口与实现相分离的类库，比如JDBC类库，在编译之时仅依赖相关的接口，在具体的运行之时，才需要具体的mysql、oracle等等数据的驱动程序。此类的驱动都是为runtime的类库。 |
| provided | 该依赖在打包过程中，不需要打进去，这个由运行的环境来提供，比如tomcat或者基础类库等等，事实上，该依赖可以参与编译、测试和运行等周期，与compile等同。区别在于打包阶段进行了exclude操作。 |
| system   | 使用上与provided相同，不同之处在于该依赖不从maven仓库中提取，而是从本地文件系统中提取，其会参照systemPath的属性进行提取依赖。 |
| import   | 这个是maven2.0.9版本后出的属性，import只能在dependencyManagement的中使用，能解决maven单继承问题，import依赖关系实际上并不参与限制依赖关系的传递性。 |

> ### 2 添加打包插件 maven-compiler-plugin

```xml
<plugin>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <encoding>UTF-8</encoding>
        <compilerArguments>
            <!--扩展依赖库-->
            <extdirs>lib</extdirs>
        </compilerArguments>
    </configuration>
</plugin>
```

> ### 3 通过maven指令给jar包安装到

```shell
mvn install:install-file -Dfile=sdk-1.0.jar -DgroupId=com.example -DartifactId=sdk -Dversion=1.0 -Dpackaging=jar
```

| 参数         | 说明              |
| ------------ | ----------------- |
| -Dfile       | 指明本地jar包路径 |
| -DgroupId    | 项目包            |
| -DartifactId | 项目名            |
| -Dversion    | 版本号            |
| -Dpackaging  | 包类型            |

> 在pom中引入

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>sdk</artifactId>
    <version>1.0</version>
</dependency>
```
