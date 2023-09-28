> ## SpringData procedure

> 存储过程内容如下

```sql
PROCEDURE pro_test_call (iStr IN VARCHAR2, oStr OUT VARCHAR2) AS
BEGIN
  oStr := 'Hello ::' || iStr;
END pro_test_call_str;
```

> 在实体类上配置`@NamedStoredProcedureQueries`

```java
@Entity
@Table(name = "tb_user")
@NamedStoredProcedureQueries({
    @NamedStoredProcedureQuery(name = "callTest", procedureName = "proName", parameters = {
        @StoredProcedureParameter(mode = ParameterMode.IN, name = "iStr", type = String.class),
        @StoredProcedureParameter(mode = ParameterMode.OUT, name = "oStr", type = String.class)
    })
})
public class User implements Serializable {}
```

> @NamedStoredProcedureQuery`定义一个存储过程`
>
> > `name` : 在程序中的名字
>
> > `procedureName` : 数据库存储过程路径(可以`package.procedure`)
>
> > `parameters` : 存储过程参数
>
> ---
>
> >  @StoredProcedureParameter`存错过程参数`
> >
> > > `mode` :: 参数类型 ParameterMode.[ `IN` | `OUT` | `INOUT` ]
> >
> > > `name` :: 参数名
> >
> > > `type` :: 参数类型 :: `type.class`

> 编写`dao`层接口

```java
@Repository
public interface UserCall extends JpaRepository<User, Integer> {

    /** 方法调用存储过程带 OUT 参数 */
    @Procedure(name = "callTest")
    String callString(@Param("iStr") String iStr);

    /** 调用存错过程不带OUT */
    @Procedure(procedureName = "pro_user_insert")
    void add(@Param("i_name") String name, @Param("i_pwd") String pwd);

}
```

> 编写`controller`接口

```java
@RestController
public class CallController {

    @Autowired
    private UserCall userCall;

    @GetMapping("/call")
    public String mark(String str) {
        String callString = userCall.callString(str);
        return callString;
    }

}
```

> 如果通过 junit 运行出现异常

```java
Hibernate: {call procedure_package.pro_test_call_str(?,?)}

org.springframework.dao.InvalidDataAccessApiUsageException: OUT/INOUT parameter not available: oStr; nested exception is java.lang.IllegalArgumentException: OUT/INOUT parameter not available: oStr
```

> `解决方法`: 直接启动 SpringBoot 应用通过 Controller 接口访问方法