> ### Spring-SpEL

> 语法: `#{表达式}`

```xml
<!-- 引用对象 -->
<property name="object" value="#{object}"></property>
<!-- 引用对象.属性 -->
<property name="attr" value="#{object.attr}"></property>
<!-- 引用对象.属性.方法 -->
<property name="attr" value="#{object.attr.toString()}"></property>
```

> 支持运算符

1. [ + , - , * , / , % , ^ ]
2. [ < , > , == , <= , >= , lt , gt , eq , le , ge ]

```xml
<!-- 字符串拼接 -->
<property name="attr" value="#{object.name + ':' + object.name}"></property>
<!-- 调用静态 [ 方法, 属性 ] -->
<property name="attr" value="#{T(java.lang.Math).PI}"></property>
<!-- 使用三元运算符 -->
<property name="attr" value="#{boolean?value1:value2}"></property>
```

