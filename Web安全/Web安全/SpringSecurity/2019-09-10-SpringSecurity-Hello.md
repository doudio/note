> ## SpringSecurity 权限验证

> 导入 Security 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

> 添加 Scurity 配置类

```java
@Component
public class ScurityConfig extends WebSecurityConfigurerAdapter {

    //登录成功handler
    @Resource
    private LoginSuccessHandler loginSuccessHandler;

    //登录失败handler
    @Resource
    private LoginFailureHandler loginFailureHandler;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            //permitAll开放接口
            .antMatchers("/welcome").permitAll()
            //hasAnyRole需要有 role 权限才能访问
            .antMatchers("/admin").hasAnyRole("admin");

        //基础设置
        http.httpBasic()//配置HTTP基本身份验证
            .and()
            .authorizeRequests()
            .anyRequest().authenticated()//所有请求都需要认证
            .and()
            .formLogin() //登录表单
            .loginPage("/login.html")//登录页面url
            .loginProcessingUrl("/login")//登录验证url
            .defaultSuccessUrl("/index")//成功登录跳转
            .successHandler(loginSuccessHandler)//成功登录处理器
            .failureHandler(loginFailureHandler)//失败登录处理器
            .permitAll();//登录成功后有权限访问所有页面

        //关闭csrf跨域攻击防御
        http.csrf().disable();
    }

    @Autowired
    private LoginAuthenticationProvider loginAuthenticationProvider;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.authenticationProvider(loginAuthenticationProvider);
    }

    /**
     * 配置加密方式为 BCrypt加密
     */
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

}
```

* 踩坑注意在 hasAnyRole("admin"); 会默认给你加上 `ROLE_` 作为前缀

> 编写逻辑验证用户登录

```java
@Component
public class LoginAuthenticationProvider implements AuthenticationProvider {
    @Override
    public Authentication authenticate(Authentication authentication) 
        throws AuthenticationException {
        // 传入的用户名
        String username = authentication.getName();
        // 传入的密码
        String rawPassword = (String) authentication.getCredentials();

        //查询
        User user = new User();
        user.setUsername(username);
        user.setPassword(rawPassword);
		// 初始化用户的 Role 权限
        List<SimpleGrantedAuthority> collect =
            Arrays.asList(rawPassword.split(","))
            .stream().map(e -> new SimpleGrantedAuthority(e))
            .collect(Collectors.toList());
        
        if ("password".equals(username)) {
            throw new LockedException("该账号已被锁定");
        }
        
        return new UsernamePasswordAuthenticationToken(user, rawPassword, collect);
    }
    
    /**
    * 确保authentication采用的是UsernamePasswordAuthenticationToken
    */
    @Override
    public boolean supports(Class<?> authentication) {
        return authentication.equals(UsernamePasswordAuthenticationToken.class);
    }
    
}
```
* ##### Security 已经定义好的基本异常

* DisabledException("该账户已被禁用，请联系管理员");

* LockedException("该账号已被锁定");

* AccountExpiredException("该账号已过期，请联系管理员");

* CredentialsExpiredException("该账户的登录凭证已过期，请重新登录");

* BadCredentialsException("输入密码错误!");

> 编写 登录成功 & 登录失败的 header

```java
/**
 * 登陆成功处理handler
 */
@Component
public class LoginSuccessHandler extends SavedRequestAwareAuthenticationSuccessHandler {

    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException {
        Map<String, Object> paramMap = new HashMap<>();
        paramMap.put("code", "400");
        paramMap.put("message", "登录成功!");
        response.setContentType("application/json;charset=utf-8");
        PrintWriter out = response.getWriter();
        out.write(JSONObject.toJSONString(paramMap));
        out.flush();
        out.close();
    }

}
```

```java
/**
 *  登陆失败处理handler
 **/
@Component
public class LoginFailureHandler extends SimpleUrlAuthenticationFailureHandler {

    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException {
        Map<String, Object> paramMap = new HashMap<>();
        paramMap.put("code", "500");
        paramMap.put("message", exception.getMessage());
        response.setContentType("application/json;charset=utf-8");
        PrintWriter out = response.getWriter();
        out.write(JSONObject.toJSONString(paramMap));
        out.flush();
        out.close();
    }

}
```

> ##### 通过注解来给 controller 设置权限

* 在 SecurityConfig 类上添加注解

```java
@Component
@EnableGlobalMethodSecurity(prePostEnabled = true,securedEnabled=true,jsr250Enabled=true)
public class ScurityConfig extends WebSecurityConfigurerAdapter { }
```

* 编写 controller 使用注解

```java
@RestController
public class AuthorityController {

    @GetMapping("admin")
    public String admin() {
        return "admin";
    }

    @GetMapping("test")
    @RolesAllowed({"test", "prod"})
    public String prod() {
        return "prod";
    }

}
```

