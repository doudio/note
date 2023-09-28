> ## LocalDateTime API

> #### 对象介绍

| Object        | desc     | format                  |
| ------------- | -------- | ----------------------- |
| LocalDateTime | 日期时间 | `yyyy-MM-dd'T'HH:mm:ss` |
| LocalDate     | 日期     | `yyyy-MM-dd`            |
| LocalTime     | 时间     | `HH:mm:ss`              |

> #### 获取对象实例

| Function | Desc         |
| -------- | ------------ |
| `now`    | 获取当前时间 |
| `of`     | 获取指定时间 |

> `LocalDateTime` 与 `String` 相互转换

```java
LocalDate date = LocalDate.now();
String text = date.format(formatter);
LocalDate parsedDate = LocalDate.parse(text, formatter);
```

> #### 判断 `Prefix` `is`

| Suffix      | desc                                     |
| ----------- | ---------------------------------------- |
| `after`     | 是否在传入对象, 之后                     |
| `before`    | 是否在传入对象, 之前                     |
| `equal`     | 相等                                     |
| `supported` | 检查是否受支持( `字段支持`, `单位支持` ) |

> ##### Demo

```java
LocalDateTime maxTime = LocalDateTime.now().plusSeconds(SendMessageConfig.getDelayTime());

if (messageDTO.getSendTime().isAfter(maxTime)) {
    return ViewUtils.build(ViewCodeEnum.ERROR.getCode(), "验证码超时", null);
}
```

> #### 运算 `Operation`

> `Prefix` `plus` 加

> `Prefix` `minus` 减

> `prefix` `get` 获取

> #### 时间单位

| `Suffix`  | Desc |
| --------- | ---- |
| `nanos`   | 纳   |
| `seconds` | 秒   |
| `hours`   | 小时 |
| `minutes` | 分钟 |
| `days`    | 天   |
| `weeks`   | 周   |
| `months`  | 月   |
| `years`   | 年   |

> #### 获取时间戳

```java
Instant.now().toEpochMilli()

System.currentTimeMillis()
```

---

> 将 Date 加一天

```java
Calendar instance = Calendar.getInstance();
instance.setTime(new Date());
instance.add(Calendar.DAY_OF_MONTH, 1);
System.out.println(instance.getTime());
```

> 最基础的 Date 转换

```java
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");    
try {    
    return dateFormat.parse(source);    
} catch (ParseException e) {    
    e.printStackTrace();    
}
```

> LocalDateTime 与毫秒值需要使用到的 API

> 格式化转换

```java
// 将毫秒值转换为 LocalDateTime 
public static LocalDateTime toLocalDateTime(long time) {
    return LocalDateTime.ofEpochSecond(time, 0, ZoneOffset.ofHours(8));
}

// String 转 LocalDateTime
public static LocalDateTime toLocalDateTime(String time) {
    DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    return LocalDateTime.parse(time, dtf);
}

// LocalDateTime 转 String
public static String timToString(LocalDateTime time) {
    DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    return dtf.format(time);
}
```

> 计算两个时间的差

```java
Duration duration = Duration.between(start,end);
duration.toMinutes();// 分钟
duration.getSeconds();// 秒
```