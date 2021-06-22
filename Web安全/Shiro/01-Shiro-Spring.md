> Shiro 的使用

1. 导入`jar`包
2. 添加`shiro`的过滤器
3. 在`spring`环境中配置`shiro`

> 导入`jar`包

```xml
<!-- shiro - start -->
<dependency>
	<groupId>org.apache.shiro</groupId>
	<artifactId>shiro-all</artifactId>
	<version>1.3.2</version>
</dependency>

<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.5</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.12</version>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.7</version>
</dependency>

<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.2</version>
</dependency>

<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache-core</artifactId>
    <version>2.6.11</version>
</dependency>

<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>2.2.2</version>
</dependency>

<dependency>
    <groupId>org.aopalliance</groupId>
    <artifactId>com.springsource.org.aopalliance</artifactId>
    <version>1.0.0</version>
</dependency>

<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>com.springsource.org.aspectj.weaver</artifactId>
    <version>1.6.4.RELEASE</version>
</dependency>
<!-- shiro - end -->
```

> 添加`shiro`的过滤器

```xml
<!-- shiro 的入口 最好先配置 shiro -->
<filter>
    <filter-name>shiroFilter</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
    <init-param>
        <param-name>targetFilterLifecycle</param-name>
        <param-value>true</param-value>
    </init-param>
    <init-param>
        <param-name>targetBeanName</param-name>
        <param-value>shiroFilter</param-value>
    </init-param>
</filter>

<filter-mapping>
    <filter-name>shiroFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

> 在`spring`环境中配置`shiro`

```xml
<!-- 1. 配置 SecurityManager! -->
<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
    <!-- 配置缓存管理器 -->
    <property name="cacheManager" ref="cacheManager" />
    <!-- 配置realm进行验证 -->
    <property name="realm" ref="jdbcRealm"></property>
    <!-- 记住我时间 -->
    <property name="rememberMeManager.cookie.maxAge" value="10"></property>
</bean>
```

* 单个Realm 验证需要: `<property name="realm" ref="jdbcRealm"></property>`
* 配置多个Realm: `<property name="authenticator" ref="authenticator"></property>`
  * 或者: `<property name="realms"></property>`

> 配置`ehcache`缓存

```xml
<!-- 配置 CacheManager. 需要加入 ehcache 的 jar 包及配置文件. -->
<bean id="cacheManager" class="org.apache.shiro.cache.ehcache.EhCacheManager">
    <property name="cacheManagerConfigFile" value="classpath:ehcache.xml" />
</bean>
```

> `ehcache.xml`

```xml
<ehcache>

    <diskStore path="java.io.tmpdir"/>
    
    <cache name="authorizationCache"
           eternal="false"
           timeToIdleSeconds="3600"
           timeToLiveSeconds="0"
           overflowToDisk="false"
           statistics="true">
    </cache>

    <cache name="authenticationCache"
           eternal="false"
           timeToIdleSeconds="3600"
           timeToLiveSeconds="0"
           overflowToDisk="false"
           statistics="true">
    </cache>

    <cache name="shiro-activeSessionCache"
           eternal="false"
           timeToIdleSeconds="3600"
           timeToLiveSeconds="0"
           overflowToDisk="false"
           statistics="true">
    </cache>
    
    <defaultCache
        maxElementsInMemory="10000"
        eternal="false"
        timeToIdleSeconds="120"
        timeToLiveSeconds="120"
        overflowToDisk="true"
        />
    
    <cache name="sampleCache1"
        maxElementsInMemory="10000"
        eternal="false"
        timeToIdleSeconds="300"
        timeToLiveSeconds="600"
        overflowToDisk="true"
        />

</ehcache>

```

> 配置`realm`

```xml
<bean id="jdbcRealm" class="com.znsd.shiro.realms.ShiroRealm">
    <property name="credentialsMatcher">
        <!-- 使用 MD5 进行加密 -->
        <bean class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
            <!-- 加密方式 -->
            <property name="hashAlgorithmName" value="MD5"></property>
            <!-- 加密次数 -->
            <property name="hashIterations" value="1024"></property>
        </bean>
    </property>
</bean>
```

> `com.znsd.shiro.realms.ShiroRealm`

```java
public class ShiroRealm extends /*AuthenticatingRealm*/ AuthorizingRealm {

	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
		// 1. 把 AuthenticationToken 转换为 UsernamePasswordToken
		UsernamePasswordToken upToken = (UsernamePasswordToken) token;

		// 2. 从 UsernamePasswordToken 中来获取 username
		String username = upToken.getUsername();

		// 3. 调用数据库的方法, 从数据库中查询 username 对应的用户记录
		System.out.println("从数据库中获取 username: " + username + " 所对应的用户信息.");

		// 4. 若用户不存在, 则可以抛出 UnknownAccountException 异常
		if ("unknown".equals(username)) {
			throw new UnknownAccountException("用户不存在!");
		}

		// 5. 根据用户信息的情况, 决定是否需要抛出其他的 AuthenticationException 异常.
		if ("monster".equals(username)) {
			throw new LockedAccountException("用户被锁定");
		}

		// 6. 根据用户的情况, 来构建 AuthenticationInfo 对象并返回. 通常使用的实现类为: SimpleAuthenticationInfo
		// 以下信息是从数据库中获取的.
		// 1). principal: 认证的实体信息. 可以是 username, 也可以是数据表对应的用户的实体类对象.
		Object principal = username;
		// 2). credentials: 密码.
		Object credentials = null; // "fc1709d0a95a6be30bc5926fdb7f22f4";
		if ("admin".equals(username)) {
			credentials = "038bdaf98f2037b31f1e75b5b4c9b26e";
		} else if ("user".equals(username)) {
			credentials = "098d2c478e9c11555ce2823231e02ec1";
		}

		// 3). realmName: 当前 realm 对象的 name. 调用父类的 getName() 方法即可
		String realmName = getName();
		// 4). 盐值.
		ByteSource credentialsSalt = ByteSource.Util.bytes(username);

		SimpleAuthenticationInfo info = null; // new SimpleAuthenticationInfo(principal, credentials, realmName);
		info = new SimpleAuthenticationInfo(principal, credentials, credentialsSalt, realmName);
		return info;
	}

	// 授权会被 shiro 回调的方法
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
		// 1. 从 PrincipalCollection 中来获取登录用户的信息
		Object principal = principals.getPrimaryPrincipal();

		// 2. 利用登录的用户的信息来用户当前用户的角色或权限(可能需要查询数据库)
		Set<String> roles = new HashSet<>();
		roles.add("user");
		if ("admin".equals(principal)) {
			roles.add("admin");
		}

		// 3. 创建 SimpleAuthorizationInfo, 并设置其 reles 属性.
		SimpleAuthorizationInfo info = new SimpleAuthorizationInfo(roles);

		// 4. 返回 SimpleAuthorizationInfo 对象.
		return info;
	}

}
```

> 用户登录方法

```java
@RequestMapping("/login")
public String login(@RequestParam("account") String account, 
                    @RequestParam("pwd") String pwd) {
    // 1. 获取当前的 Subject. 调用 SecurityUtils.getSubject();
    Subject currentUser = SecurityUtils.getSubject();

    // 2. 测试当前的用户是否已经被认证. 即是否已经登录. 调用 Subject 的 isAuthenticated() 
    if (!currentUser.isAuthenticated()) {
        // 3. 若没有被认证, 则把用户名和密码封装为 UsernamePasswordToken 对象
        UsernamePasswordToken token = new UsernamePasswordToken(account, pwd);
        token.setRememberMe(true);
        try {
            // 4. 执行登录: 调用 Subject 的 login(AuthenticationToken) 方法. 
            currentUser.login(token);
        }
        catch (AuthenticationException ae) {
            System.out.println("登录失败: " + ae.getMessage());
        }
    }
    /**
	 * 注意要重定向去目标页面, 转发属于同一个请求了
	 */
    return "redirect:/success.jsp";
}
```

> 配置: `lifecycleBeanPostProcessor`

```xml
<!-- 可以自定义的来调用配置在 Spring IOC 容器中 shiro bean 的声明周期方法 -->
<bean id="lifecycleBeanPostProcessor"
class="org.apache.shiro.spring.LifecycleBeanPostProcessor" />
```

> 使用 Shiro 注解

```xml
<!-- 启用 IOC 容器中使用 shiro 的注解. 但必须在`配置了 lifecycleBeanPostProcessor 之后才可以使用 -->
<bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" depends-on="lifecycleBeanPostProcessor" />

<bean class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
    <property name="securityManager" ref="securityManager" />
</bean>
```

> 配置 shiroFilter

```xml
<!-- bean id 必须与 shiro 过滤器名一致 若不一致则配置过滤器的 init 参数 -->
<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
    <property name="securityManager" ref="securityManager" />
    <!-- 登录页面 -->
    <property name="loginUrl" value="/login.jsp" />
    <!-- 没有权限的页面 -->
    <property name="unauthorizedUrl" value="/unauthorized.jsp" />

    <!-- 
   访问模式
   [anon : 可自由访问的]
   [authc : 需要认证后才能访问]
   [logout : 退出登录]

   url匹配模式
   [? : 一个字符]
   [* : 匹配零个或多个]
   [** : 匹配多重路径]
   url权限从上往下依次匹配
   -->
    <!-- 配置页面权限 -->
    <property name="filterChainDefinitionMap" ref="filterChainDefinitionMap" />
</bean>

<!-- 配置一个 bean, 该 bean 实际上是一个 Map. 通过实例工厂方法的方式 -->
<bean id="filterChainDefinitionMap" factory-bean="filterChainDefinitionMapBuilder" factory-method="buildFilterChainDefinitionMap"></bean>
    
<bean id="filterChainDefinitionMapBuilder" class="com.znsd.shiro.factory.FilterChainDefinitionMapBuilder"></bean>
```

> `com.znsd.shiro.factory.FilterChainDefinitionMapBuilder`

```java
public class FilterChainDefinitionMapBuilder {

	public LinkedHashMap<String, String> buildFilterChainDefinitionMap(){
		LinkedHashMap<String, String> map = new LinkedHashMap<>();
		
		map.put("/login.jsp", "anon");
		map.put("/shiro/login", "anon");
		map.put("/shiro/logout", "logout");
		map.put("/user.jsp", "authc,roles[user]");
		map.put("/admin.jsp", "authc,roles[admin]");
		map.put("/list.jsp", "user");
		
		map.put("/**", "authc");
		
		return map;
	}
	
}
```

> 访问模式

| 值     | 描述               |
| ------ | ------------------ |
| anon   | 可自由访问的       |
| authc  | 需要认证后才能访问 |
| logout | 退出登录           |

> url匹配模式

| 值   | 描述           |
| ---- | -------------- |
| ?    | 一个字符       |
| *    | 匹配零个或多个 |
| **   | 匹配多重路径   |

