> #### Ant Hello

1. 下载[ANT](http://ant.apache.org/bindownload.cgi)
2. 配置ANT环境变量
   1. `ANT_MOHE` = `${安装ant的根路径}`
   2. `path` = `%ANT_MOHE%\bin`
3. 打开`cmd`输入`ant`

> 打开`cmd`输入`ant` : `ant`配置成功

```xml-dtd
Microsoft Windows [版本 10.0.16299.15]
(c) 2017 Microsoft Corporation。保留所有权利。

C:\Users\Administrator>ant
Buildfile: build.xml does not exist!
Build failed
```

> 在这里运行`ant`命令时抛出: 找不到`build.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="DemoAnt" default="main" basedir=".">
     <!-- 初始化：创建目录，清理目录等 -->
     <target name="init">
           <echo>start initing ... </echo>
           <echo>finish initing. </echo>
     </target>
	 
     <!-- 打包过程,默认值 -->
     <target name="main" depends="init">
		
     </target>
     <!-- 清理不需要的生成文件等-->
     <target name="clean">
     </target>
	 
</project>
```

1. `project`:项目
   1. `name`:`ant`项目的名字随意
   2. `default`:默认执行那个`target`
2. `target`:目标 OR 对象
   1. `name`:起一个名字方便调用
   2. `depends`:他所依赖的`target`
   3. `if`:指定的某个属性是否存在, `存在`执行
   4. `unless`: 指定的某个属性是否存在, `不存在`执行
3. `echo`:输出

> `property`

```xml
<property name="key" value="value" />

<target name="main">
    <echo message="start initing ... ${key}"/>
    <echo>finish initing. ${key}</echo>
</target>
```

> 执行`ant`时的指令
>
> 指定执行那个 `xml` 文件

```xml
ant -buildfile build-test.xml
```

> 指定执行那个 `xml` 文件 , 执行一个叫做`main`的`target`

```xml
ant -buildfile build-test.xml main 
```

> -Dname=newValue: 指定将那个变量进行赋值

```xml
ant -buildfile build-test.xml -Dname=newValue clean
```

---

> 创建一个目录

```xml
<mkdir dir="${class.root}"/>
```

> move 命令：移动文件或目录

```xml
<!-- move 命令：移动文件或目录 -->
<move file="sourcefile" tofile=”destfile”/>

<!-- 移动某个目录到另一个目录 -->
<move todir="newdir">
    <fileset dir="olddir"/>
</move>
```

> echo 命令：该任务的作用是根据日志或监控器的级别输出信息
>
> 它包括 message 、 file 、 append 和 level 四个属性

```xml
<echo message="echo message" file="/logs/ant.log" append="true">
```

> `copy` 拷贝一个文件

```xml
<copy file="old.txt" tofile="new.txt"/>
```

> `copy` 目录

```xml
<copy todir="../dest_dir">
    <fileset dir="src_dir"/>
</copy>
```

> delete命令：对文件或目录进行删除

```xml
<!-- delete命令：对文件或目录进行删除 -->
<delete file="/src/springmvc.xml"/>
<!-- 删除某个目录 -->
<delete dir="/src"/>
<!-- 删除所有的jar文件或空目录 -->
<delete includeEmptyDirs="true">
    <fileset dir="." includes="*.jar"/>
</delete>
```

>  javac 标签节点元素：编译一个或一组java文件

1. srcdir表示源程序的目录
2. destdir表示class文件的输出目录
3. include表示被编译的文件的模式
4. excludes表示被排除的文件的模式
5. classpath表示所使用的类路径
6. debug表示包含的调试信息
7. optimize表示是否使用优化
8. verbose 表示提供详细的输出信息
9. fileonerror表示当碰到错误就自动停止