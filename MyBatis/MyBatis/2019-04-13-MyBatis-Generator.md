> #### MyBatis Generator

1. 找到 [MyBatis Generator](https://github.com/mybatis) 将其下载
2. 导入官方提供的`jar`:`mybatis-generator-core-1.3.7` `\` `lib`
3. 在项目根路径创建: `generatorConfig.xml`
4. 在官方找到执行的 [code](http://www.mybatis.org/generator/running/running.html)

> 在项目根路径创建: `generatorConfig.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
	
  <!-- targetRuntime : 配置模板, 模板有简单版的 和 带条件查询版的 -->
  <context id="DB2Tables" targetRuntime="MyBatis3">
    <!-- jdbc的一些配置 -->
    <jdbcConnection driverClass="com.mysql.jdbc.Driver"
        connectionURL="jdbc:mysql://localhost:3306/my_batis"
        userId="root"
        password="root">
    </jdbcConnection>

    <javaTypeResolver >
      <property name="forceBigDecimals" value="false" />
    </javaTypeResolver>

	<!-- 指定javaBean策略 -->
	<!-- targetPackage: javaBean包名 -->
	<!-- targetProject: javaBean存放的路径 -->
    <javaModelGenerator targetPackage="com.znsd.mybatis.bean" targetProject=".\src\main\java">
      <property name="enableSubPackages" value="true" />
      <property name="trimStrings" value="true" />
    </javaModelGenerator>

	<!-- javaBean 对应的 mapper.xml : 配置文件 -->
    <sqlMapGenerator targetPackage="com.znsd.mybatis.dao"  targetProject=".\src\main\java">
      <property name="enableSubPackages" value="true" />
    </sqlMapGenerator>

	<!-- javaBean 对应的 mapper.java : 接口 -->
    <javaClientGenerator type="XMLMAPPER" targetPackage="com.znsd.mybatis.dao"  targetProject=".\src\main\java">
      <property name="enableSubPackages" value="true" />
    </javaClientGenerator>

	<!-- 生成那些数据库表 -->
    <!-- tableName : 表明 -->
    <!-- domainObjectName : javaBean 名 -->
	<table tableName="tbl_department" domainObjectName="Department"></table>
	<table tableName="tbl_employee" domainObjectName="Employee"></table>

  </context>
</generatorConfiguration>
```

> 在官方找到执行的 [code](http://www.mybatis.org/generator/running/running.html)

```java
List<String> warnings = new ArrayList<String>();
boolean overwrite = true;
File configFile = new File("generatorConfig.xml");
ConfigurationParser cp = new ConfigurationParser(warnings);
Configuration config = cp.parseConfiguration(configFile);
DefaultShellCallback callback = new DefaultShellCallback(overwrite);
MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
myBatisGenerator.generate(null);
```

