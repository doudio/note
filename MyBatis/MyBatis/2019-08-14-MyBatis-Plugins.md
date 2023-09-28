> #### MyBatis Plugins

> 插件（plugins）
>
> ​	MyBatis 允许你在已映射语句执行过程中的某一点进行拦截调用。默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：

- `Executor` (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)
  - 中定义了一些[查询 | 修改]事务处理
- `ParameterHandler` (getParameterObject, setParameters)
  - 参数处理程序设置准备语句的参数。
- `ResultSetHandler` (handleResultSets, handleOutputParameters)
- `StatementHandler` (prepare, parameterize, batch, update, query)
- `MetaObject` `metaObject` = `SystemMetaObject`.`forObject` `(` `target` `)`;

---

> Demo

```java
@Intercepts({@Signature(type = StatementHandler.class, method = "prepare", args = { Connection.class, Integer.class }) })
public class PagePlugins implements Interceptor {

	/**
	 * 获取执行的sql
	 */
	private static final String SQL_KEY = "delegate.boundSql.sql";
	
	/**
	 * 获取方法入参
	 */
	private static final String PARAM_KEY = "parameterHandler.parameterObject";

	@SuppressWarnings("unchecked")
	public Object intercept(Invocation invocation) throws Throwable {
		StatementHandler target = (StatementHandler) invocation.getTarget();
		// 拿到目标对象的元数据
		MetaObject metaObject = SystemMetaObject.forObject(target);

		String sql = metaObject.getValue(SQL_KEY).toString();
		
		// 获取目标方法的入参
		Map<String, Object> map = (Map<String, Object>) metaObject.getValue(PARAM_KEY);
		Integer curr = Integer.valueOf(map.get("curr").toString());
		Integer limit = Integer.valueOf(map.get("limit").toString());
		if (curr == null || limit == null) return invocation.proceed();
		
		metaObject.setValue(SQL_KEY, sql += "LIMIT " + ((curr - 1) * limit) + ", " + limit);
		
		return invocation.proceed();
	}

	public Object plugin(Object target) {
		System.out.println("PagePlugins.plugin(Object)");
		return Plugin.wrap(target, this);
	}

	public void setProperties(Properties properties) {
		System.out.println("PagePlugins.setProperties(Properties)");
	}

}

```

---

> #### 分页插件

1. 添加`maven`配置
2. 在`mybatis`配置文件中配置插件
3. 获取`page`对象: [官网文档](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/zh/HowToUse.md)

> 添加`maven`配置

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.2</version>
</dependency>
```

> 在`mybatis`配置文件中配置插件

```xml
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

> 获取`page`对象

```java
DepartmentMapper mapper = session.getMapper(DepartmentMapper.class);
		
Page<Object> page = PageHelper.startPage(1, 10);

List<Department> deps = mapper.findAllDep();
deps.stream().forEach(System.out::println);

System.out.println("getPageNum: " + page.getPageNum());
System.out.println("getTotal: " + page.getTotal());
System.out.println("getPages: " + page.getPages());
```

