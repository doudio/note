> ## Guns Hello

> 解压`Guns-master.zip`项目导入`IDE`工具

> 初始化库: 找到`Guns-master\guns-admin\sql\guns.sql`全部执行

> 在`guns-admin`项目的`application.yml`文件中进行配置

```yaml
server:
  port: 8080

guns:
  swagger-open: true              #是否开启swagger (true/false)
  kaptcha-open: false             #是否开启登录时验证码 (true/false)
#  file-upload-path: d:/tmp       #文件上传目录(不配置的话为java.io.tmpdir目录)
  spring-session-open: false      #是否开启spring session,如果是多机环境需要开启(true/false)
  session-invalidate-time: 1800     #session失效时间(只在单机环境下生效，多机环境在SpringSessionConfig类中配置) 单位：秒
  session-validation-interval: 900  #多久检测一次失效的session(只在单机环境下生效) 单位：秒

spring:
  profiles:
    active: @spring.active@
  mvc:
    static-path-pattern: /static/**
    view:
      prefix: /WEB-INF/view
  devtools:
    restart:
      enabled: false
      additional-paths: src/main/java
      exclude: static/**,WEB-INF/view/**
  servlet:
    multipart:
      max-request-size: 100MB
      max-file-size: 100MB

mybatis-plus:
  typeAliasesPackage: com.stylefeng.guns.modular.system.model

log:
  path: guns-logs

---

spring:
  profiles: local
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/guns?autoReconnect=true&useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=CONVERT_TO_NULL&useSSL=false&serverTimezone=UTC
    username: root
    password: root
    db-name: guns #用来搜集数据库的所有表
    filters: wall,mergeStat

#多数据源情况的配置
guns:
  muti-datasource:
    open: false
    url: jdbc:mysql://127.0.0.1:3306/guns_test?autoReconnect=true&useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=CONVERT_TO_NULL&useSSL=false&serverTimezone=UTC
    username: root
    password: root
    dataSourceNames:
      - dataSourceGuns
      - dataSourceBiz

---

spring:
  profiles: dev
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/guns?autoReconnect=true&useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=CONVERT_TO_NULL&useSSL=false&serverTimezone=UTC
    username: root
    password: root
    db-name: guns #用来搜集数据库的所有表
    filters: wall,mergeStat

---

spring:
  profiles: test
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/guns?autoReconnect=true&useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=CONVERT_TO_NULL&useSSL=false&serverTimezone=UTC
    username: root
    password: root
    filters: wall,mergeStat

---

spring:
  profiles: produce
  datasource:
      url: jdbc:mysql://127.0.0.1:3306/guns?autoReconnect=true&useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=CONVERT_TO_NULL&useSSL=false&serverTimezone=UTC
      username: root
      password: root
      filters: wall,mergeStat
```

> 修改: 
>
> > `@spring.active@` : 选择一个开发环境 : `dev`
>
> > 更改`dev`环境下的`mysql`数据库配置

> 启动`Guns`项目::`GunsApplication.java`

> 访问: `http://localhost:8080/`

| 账号    | 密码     |
| ------- | -------- |
| `admin` | `111111` |

