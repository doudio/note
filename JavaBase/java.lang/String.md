> ## Java String 对象

> #### 特点：

> String 属于 Java 中的引用数据类型

> 在相同的字符串 "" 时 String 对象会被放在常量池中保存

> String 部分实现

> Equals()

```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

> ### 收藏的正则表达式

> 匹配规则：字符串中必须包含（字符,数字,特殊字符）

```java
^(?:(?=.*[0-9].*)(?=.*[A-Za-z].*)(?=.*[\W].*))[\W0-9A-Za-z]{3,}$
```

> Email

```java
^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$
```

> 手机号码

```java
^(13[0-9]|14[5|7]|15[0|1|2|3|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$
```

> 腾讯QQ

```java
[1-9][0-9]{4,}
```

