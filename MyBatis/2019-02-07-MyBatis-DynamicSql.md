> #### MyBatis DynamicSql

* `where`: 封装查询条件
* `set`: 封装修改条件
* `if`:条件成立拼接 `SQL`
* `choose (when, otherwise)`:与`java`中`swtich`思想差不多
* `trim`:字符串截取
* `foreach`:遍历集合拼接`SQL`
* `MyBatis`:两个内置参数
* `bind`:标签
* `sql`:标签

> `set`: 封装修改条件

```xml
<set>
    <if test="true|false">
        SQL-fragment
    </if>
    <if test="true|false">
        SQL-fragment
    </if>
</set>
```

> `if`:条件成立拼接 `SQL`

```xml
<select id="findByEmpDySql" resultType="com.znsd.mybatis.bean.Employee">
    SELECT * FROM `tbl_employee`<!--  WHERE  -->
    <where>
        <if test="id != null">
            `id` = #{id}
        </if>
        <if test="lastName != null and lastName.trim() != ''">
            AND `last_name` LIKE #{lastName} 
        </if>
        <if test="gender == 0 or gender == 1">
            AND `gender` = #{gender}
        </if>
        <if test="email != null and email.trim() != ''">
            AND `email` = #{email}
        </if>
    </where>
</select>
```

1. 在动态生成 SQL 语句中 , 如果不传入 ID 则 SQL 语句拼接语法错误
   1. 解决方案: SELECT * FROM `tbl_employee` WHERE 1 = 1
2. 后面条件全部跟上 : AND 开头
   1. 解决方案: 使用 `<where></where>` 标签将条件包裹起来

> `choose (when, otherwise)`:与`java`中`swtich`思想差不多

```xml
<where>
    <choose>
        <when test="true|false">
            SQL-fragment
        </when>
        <when test="true|false">
			SQL-fragment
        </when>
        <otherwise>
            SQL-fragment
        </otherwise>
    </choose>
</where>
```

> `trim`:字符串截取(`where`(封装查询条件), `set`(封装修改条件))

```xml
<trim prefix="where" suffixOverrides="and">
    <if test="id!=null">
        id=#{id} and
    </if>
    <if test="lastName!=null &amp;&amp; lastName!=&quot;&quot;">
        last_name like #{lastName} and
    </if>
    <if test="email!=null and email.trim()!=&quot;&quot;">
        email=#{email} and
    </if> 
    <!-- ognl会进行字符串与数字的转换判断  "0"==0 -->
    <if test="gender==0 or gender==1">
        gender=#{gender}
    </if>
</trim>
```

> 自定义字符串的截取规则

1. 后面多出的`and`或者`or where`标签不能解决 
2. `prefix=""`:给拼串后的整个字符串加一个前缀
3. `prefixOverrides=""`:前缀覆盖： 去掉整个字符串前面多余的字符
4. `suffix=""`:给拼串后的整个字符串加一个后缀
5. `suffixOverrides=""`:后缀覆盖：去掉整个字符串后面多余的字符

> `foreach`:遍历集合拼接`SQL`

```xml
<select id="getEmp" resultType="com.znsd.mybatis.bean.Employee">
    select * from tbl_employee
    <foreach collection="ids" item="item_id" separator=","
             open="where id in(" close=")">
        #{item_id}
    </foreach>
</select>
```

> collection：指定要遍历的集合：

1. `list`类型的参数会特殊处理封装在`map`中，`map`的`key`就叫`list`
2. `item`：将当前遍历出的元素赋值给指定的变量
3. `separator`:每个元素之间的分隔符
4. `open`：遍历出所有结果拼接一个开始的字符
5. `close`:遍历出所有结果拼接一个结束的字符
6. `index`:索引。遍历list的时候是`index`就是索引，`item`就是当前值
7. **遍历map的时候`index`表示的就是`map`的`key`，`item`就是`map`的值**
8. **`#{变量名}`就能取出变量的值也就是当前遍历出的元素**

---

> `MyBatis`两个内置参数：不只是方法传递过来的参数可以被用来判断，取值。

* mybatis默认还有两个内置参数：
  * `_parameter`:代表整个参数
  * 单个参数：`_parameter`就是这个参数
  * 多个参数：参数会被封装为一个map；_parameter就是代表这个map
* `_databaseId`:如果配置了`databaseIdProvider`标签。
  * `_databaseId`就是代表当前数据库的别名`oracle`

---

```xml
<!-- bind：可以将OGNL表达式的值绑定到一个变量中，方便后来引用这个变量的值 -->
<bind name="_lastName" value="'%'+lastName+'%'"/>
```

---

> 抽取可重用的sql片段。方便后面引用

```xml
<sql id="SQLFragment">
    `id`, `name`
</sql>

<select id="getEmp" resultType="com.znsd.mybatis.bean.Employee">
    select <include refid="SQLFragment"/> from tbl_employee
</select>
```

1. `sql`抽取：经常将要查询的列名，或者插入用的列名抽取出来方便引用
2. `include`来引用已经抽取的`sql`
3. `include`还可以自定义一些`property`，`sql`标签内部就能使用自定义的属性
4. `include-property`：取值的正确方式`${prop},#{不能使用这种方式}`

