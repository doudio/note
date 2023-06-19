> ### log4j 的详细使用

1. 导入 jar 包
2. 定义 log4j 配置
3. 创建对象进行使用

---

> 导入 jar 包 Maven依赖：

```xml
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
```

> 定义 log4j 配置

```properties
### set log levels ###
log4j.rootLogger=debug,stdout

### 输出到控制台 ###
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=[param:%X{param}]%d{ABSOLUTE} %5p %c{1}:%L - %m%n
```

---

> 模板方便拷贝

```properties
### 输出到控制台 ###
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout =org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern =  %d{ABSOLUTE} %5p %c{ 1 }:%L - %m%n
```

```properties
### 输出到日志文件 ###
log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = logs/log.log
log4j.appender.D.Append = true
log4j.appender.D.Threshold = DEBUG ## 输出DEBUG级别以上的日志
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
```

```properties
### 保存异常信息到单独文件 ###
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File = logs/error.log ## 异常日志文件名
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR ## 只输出ERROR级别以上的日志!!!
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
```

---

1. 输出日志等级: log4j.rootLogger = `debug`,stdout

| 等级  | 说明                                                         |
| ----- | ------------------------------------------------------------ |
| DEBUG | 指出细粒度信息事件对调试应用程序是非常有帮助的,就是输出debug的信息. |
| INFO  | 表明消息在粗粒度级别上突出强调应用程序的运行过程,就是输出提示信息. |
| WARN  | 表明会出现潜在错误的情形,就是显示警告信息.                   |
| ERROR | 指出虽然发生错误事件,但仍然不影响系统的继续运行.就是显示错误信息. |
| FATAL | 指出每个严重的错误事件将会导致应用程序的退出.                |
| ALL   | 是最低等级的,用于打开所有日志记录.                           |
| OFF   | 是最高等级的,用于关闭所有日志记录.                           |

2. 输出日志的目的地: 
   1. log4j.rootLogger = debug,`stdout`
   2. `log4j.appender.stdout`

| 值                                        | 说明                                       |
| ----------------------------------------- | ------------------------------------------ |
| org.apache.log4j.ConsoleAppender          | 控制台                                     |
| org.apache.log4j.FileAppender             | 文件                                       |
| org.apache.log4j.DailyRollingFileAppender | 每天产生一个日志文件                       |
| org.apache.log4j.RollingFileAppender      | 文件大小到达指定尺寸的时候产生一个新的文件 |
| org.apache.log4j.WriterAppender           | 将日志信息以流格式发送到任意指定的地方     |

3. 日志输出的格式: `log4j.appender.stdout.layout`

| 值                             | 说明                                   |
| ------------------------------ | -------------------------------------- |
| org.apache.log4j.HTMLLayout    | 以HTML表格形式布局                     |
| org.apache.log4j.PatternLayout | 可以灵活地指定布局模式                 |
| org.apache.log4j.SimpleLayout  | 包含日志信息的级别和信息字符串         |
| org.apache.log4j.TTCCLayout    | 包含日志产生的时间、线程、类别等等信息 |

4. 输出日志的详细内容: `log4j.appender.stdout.layout.ConversionPattern`

| 值   | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| %m   | 输出代码中指定的消息                                         |
| %p   | 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL                |
| %r   | 输出自应用启动到输出该log信息耗费的毫秒数                    |
| %c   | 输出所属的类目，通常就是所在类的全名                         |
| %t   | 输出产生该日志事件的线程名                                   |
| %n   | 输出一个回车换行符，Windows平台为`/r/n`，Unix平台为`/n`      |
| %d   | 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss , SSS}，输出类似：2002年10月18日  22 ： 10 ： 28 ， 921 |
| %l   | 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main(TestLog4.java: 10 ) |

5. 注意若 log4j 不在项目中则在项目中指定存放的目录

```xml
<context-param> 
    <param-name>log4jConfigLocation</param-name> 
    <param-value>WEB-INF/log4j.properties</param-value> 
</context-param> 
<context-param> 
    <param-name>log4jRefreshInterval</param-name> 
    <param-value>60000</param-value> 
</context-param> 
<!-- 需要添加spring-web.jar包，否则用发生错误信息 -->
<listener> 
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class> 
</listener>
```

> 创建对象进行使用

```java
public class Log4jDemo {

	private Logger logger  =  Logger.getLogger(getClass());

	public static void main(String[] args) {
		new Log4jDemo().start();
	}

	public void start() {

		MDC.put("param", "paramValues");
		logger.info("logger.info");
		logger.error("logger.error");
		logger.debug("logger.debug");

	}

}
```

