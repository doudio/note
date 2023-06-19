> #### druid

> 详细配置 `alibaba` `GitHub` [查询文档](https://github.com/alibaba/druid/wiki/%E9%A6%96%E9%A1%B5)

> 配置 web 监控 : 在配置 druid 多配置几个属性

```xml
<property name="filters" value="wall,stat"/>
<property name="proxyFilters">
    <list>
        <ref bean="stat-filter"/>
        <ref bean="log-filter"/>
    </list>
</property>
```

```xml
<!-- 慢SQL记录 -->
<bean id="stat-filter" class="com.alibaba.druid.filter.stat.StatFilter">
    <!-- 慢sql时间设置,即执行时间大于200毫秒的都是慢sql -->
    <property name="slowSqlMillis" value="200"/>
    <property name="logSlowSql" value="true"/>
</bean>
```

```xml
<bean id="log-filter" class="com.alibaba.druid.filter.logging.Log4jFilter">
    <property name="dataSourceLogEnabled" value="true" />
    <property name="statementExecutableSqlLogEnable" value="true" />
</bean>
```

> 添加 aop 切面

```xml
<bean id="druid-stat-interceptor"
      class="com.alibaba.druid.support.spring.stat.DruidStatInterceptor">
</bean>

<bean id="druid-stat-pointcut" class="org.springframework.aop.support.JdkRegexpMethodPointcut"
      scope="prototype">
    <property name="patterns">
        <list>
            <value>com.znsd.ssm.service.*</value>
            <value>com.znsd.ssm.dao.*</value>
        </list>
    </property>
</bean>

<aop:config>
    <aop:advisor advice-ref="druid-stat-interceptor" pointcut-ref="druid-stat-pointcut"/>
</aop:config>
```

> web.xml 配置

```xml
<!--druid监控页面 -->
<servlet>
    <servlet-name>DruidStatView</servlet-name>
    <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
    <init-param>
        <!-- 不允许清空统计数据 -->
        <param-name>resetEnable</param-name>
        <param-value>false</param-value>
    </init-param>
    <init-param>
        <!-- 用户名 -->
        <param-name>loginUsername</param-name>
        <param-value>admin</param-value>
    </init-param>
    <init-param>
        <!-- 密码 -->
        <param-name>loginPassword</param-name>
        <param-value>root</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>DruidStatView</servlet-name>
    <url-pattern>/druid/*</url-pattern>
</servlet-mapping>
<!--druid监控页面 -->

<!-- web 监控过滤器 -->
<filter>
    <filter-name>DruidWebStatFilter</filter-name>
    <filter-class>com.alibaba.druid.support.http.WebStatFilter</filter-class>
    <init-param>
        <param-name>exclusions</param-name>
        <param-value>/static/*,*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>DruidWebStatFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

