> ## 02 Shiro SpringBoot.md

> 导入`pom`依赖

```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.4.0</version>
</dependency>
```

> 添加`shiro`配置

```java
@Configuration
public class ShiroConfig {
    
    @Bean
    public ShiroFilterFactoryBean shirFilter(SecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        // 必须设置 SecurityManager
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        // 设置登录页面
        shiroFilterFactoryBean.setLoginUrl("/notLogin");
        // 设置没权限页面
        shiroFilterFactoryBean.setUnauthorizedUrl("/notRole");

        // 设置拦截器
        Map<String, String> filterChainDefinitionMap = new LinkedHashMap<>();
        //游客，开发权限
        filterChainDefinitionMap.put("/guest/**", "anon");
        //用户，需要角色权限 “user”
        filterChainDefinitionMap.put("/user/**", "roles[user]");
        //管理员，需要角色权限 “admin”
        filterChainDefinitionMap.put("/admin/**", "roles[admin]");
        //开放登陆接口
        filterChainDefinitionMap.put("/login", "anon");
        //其余接口一律拦截
        //主要这行代码必须放在所有权限设置的最后，不然会导致所有 url 都被拦截
        filterChainDefinitionMap.put("/**", "authc");

        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
        System.out.println("Shiro拦截器工厂类注入成功");
        return shiroFilterFactoryBean;
    }

    /**
     * 注入 securityManager
     */
    @Bean
    public SecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        // 引用自定义 Realm
        securityManager.setRealm(simpleAuthRealm());
        return securityManager;
    }

    /**
     * 自定义身份认证 Realm
     */
    @Bean
    public SimpleAuthRealm simpleAuthRealm() {
        return new SimpleAuthRealm();
    }
}
```

> 自定义 Realm 认证

```java
public class SimpleAuthRealm extends AuthorizingRealm {
    
    @Autowired
    private UserMapper userMapper;
    
    /**
     * 验证用户信息
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("————身份认证方法————");
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        String password = userMapper.getPassword(token.getUsername());
        
        if (null == password) {
            throw new AccountException("用户名不正确");
        } else if (!password.equals(new String((char[]) token.getCredentials()))) {
            throw new AccountException("密码不正确");
        }
        
        return new SimpleAuthenticationInfo(token.getPrincipal(), password, getName());
    }

    /**
     * 初始化当前用户的 Role
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        String username = (String) SecurityUtils.getSubject().getPrincipal();
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        String role = userMapper.getRole(username);
        Set<String> set = new HashSet<>();
        set.add(role);
        info.setRoles(set);
        return info;
    }
}
```

> 在`realm`中获取`session`

```java
SecurityUtils.getSubject().getSession();

---
    
SecurityUtils.getSubject().logout();
```

> shiro 配置加密方式

```java
{
    HashedCredentialsMatcher matcher = new HashedCredentialsMatcher();
    matcher.setHashAlgorithmName("MD5");
    matcher.setHashIterations(1314);
    setCredentialsMatcher(matcher);
}

@Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        log.debug("开始执行身份认证");
    UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        
    User user = userMapper.selectOne(queryWrapper);
    if (null == user) {
        throw new AccountException("用户名不正确");
    } else if (!UserStatuEnum.OK.getCode().equals(user.getStatu())){
        throw new AccountException("用户被状态波动");
    }
		
	ByteSource credentialsSalt = ByteSource.Util.bytes(String.valueOf(user.getId()));
    SimpleAuthenticationInfo authenticationInfo = new SimpleAuthenticationInfo(
    	user.getName(),
        user.getPassword(),
        credentialsSalt,
        getName()
	);
    return authenticationInfo;
}
```

> shiro 生成盐值密码

```java
String hashAlgorithmName = "MD5";
Object crdentials = "123456";
Object salt = String.valueOf(1);
int hashIterations = 1314;
Object result = new SimpleHash(hashAlgorithmName,crdentials,salt,hashIterations);
System.out.println(">>"+crdentials+">>"+hashAlgorithmName+">>"+salt+">"+hashIterations+">" + result + ":" + salt);
```

