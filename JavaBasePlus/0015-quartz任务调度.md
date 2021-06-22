> #### quartz任务调度

1. 一个定时任务需要存在
   1. 执行的任务
   2. 什么条件下执行`触发器`

> 那么开始我们的 Hello World!

1. 导入 `quartz` 任务调度 `jar`
2. 定义类实现 `Job` 接口 : 定义任务执行的方法
3. 通过`JobDetail`创建任务
4. 通过`Trigger`的实现类定义触发器
5. 创建一个`SchedulerFactory`:调度工厂
6. 获取调度程序
7. 通过`scheduleJob()`方法将 任务 和 触发器整合
8. 调用`start()`方法开启任务

> 导入 `quartz` 任务调度 `jar`

```xml
<dependency>
    <groupId>com.atlassian.scheduler</groupId>
    <artifactId>atlassian-scheduler-quartz1</artifactId>
    <version>1.6.0</version>
    <type>pom</type>
</dependency>

<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz-jobs</artifactId>
    <version>2.2.0</version>
    <type>pom</type>
</dependency>
```

> 定义类实现 `Job` 接口 : 定义任务执行的方法

```java
public class RemindJob implements Job {

	public void execute(JobExecutionContext context) throws JobExecutionException {
		System.out.println("execute(" + LocalDateTime.now() + ")");
	}

}
```

> 通过`JobDetail`创建任务

```java
/**
 * JobDetail 创建任务
 * 	public JobDetail(String name, String group, Class jobClass)
 */
JobDetail job = new JobDetail("remindJob", "group", RemindJob.class);
```

> 新版本实现: `2.3.0`

```java
JobDetail job = JobBuilder.newJob(RemindJob.class)
    .withIdentity("remindJob", "group")
    .build();
```

> 通过`Trigger`的实现类定义触发器

```java
/**
 * public CronTrigger(String name) : 若没有 name 会抛出异常
 * 	org.quartz.SchedulerException: Trigger's name cannot be null
 */
CronTrigger trigger = new CronTrigger("trigger");
trigger.setCronExpression("*/5 * * * * ?");
```

> 新版本实现: `2.3.0`

```java
Trigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("trigger", "group")
    .withSchedule(SimpleScheduleBuilder.repeatSecondlyForever(5))
    .startNow()
    .build();
```

> 实现步骤: [`4` `5` `6` `7`]

```java
SchedulerFactory sfc = new StdSchedulerFactory();
Scheduler sched = sfc.getScheduler();

sched.scheduleJob(job, trigger);
sched.start();
```

> #### Cron表达式: `秒 分 时 日 月 周 年`

| 字段名       | 允许的值         | 允许的特殊字符                  |
| ------------ | ---------------- | ------------------------------- |
| 秒           | 0-59             | `,` `-` `*` `/`                 |
| 分           | 0-59             | `,` `-` `*` `/`                 |
| 小时         | 0-23             | `,` `-` `*` `/`                 |
| 日           | 1-31             | `,` `-` `*` `?` `/` `L` `W` `C` |
| 月           | 1-12 or JAN-DEC  | `,` `-` `*` `/`                 |
| 周几         | 1-7 or SUN-SAT   | `,` `-` `*` `?` `/` `L` `C` `#` |
| 年`可选字段` | empty, 1970-2099 | `,` `-` `*` `/`                 |

> Cron 表达式字符

| 字符 | 描述                                                      |
| ---- | --------------------------------------------------------- |
| `?`  | 表示不确定的值                                            |
| `,`  | 指定数个值                                                |
| `-`  | 指定一个值的范围                                          |
| `/`  | 指定一个值的增加幅度。n/m表示从n开始，每次增加m           |
| `L`  | 用在日表示一个月中的最后一天，用在周表示该月最后一个星期X |
| `W`  | 指定离给定日期最近的工作日(周一到周五)                    |
| `#`  | 表示该月第几个周X。6#3表示该月第3个周五                   |

> Cron 案例

| 描述                                  | 写法                   |
| ------------------------------------- | ---------------------- |
| 每隔5秒执行一次                       | `*/5 * * * * ?`        |
| 每隔1分钟执行一次                     | `0 */1 * * * ?`        |
| 每天23点执行一次                      | `0 0 23 * * ?`         |
| 每天凌晨1点执行一次                   | `0 0 1 * * ?`          |
| 每月1号凌晨1点执行一次                | `0 0 1 1 * ?`          |
| 每月最后一天23点执行一次              | `0 0 23 L * ?`         |
| 每周星期天凌晨1点实行一次             | `0 0 1 ? * L`          |
| 在26分、29分、33分执行一次            | `0 26,29,33 * * * ?`   |
| 每天的0点、13点、18点、21点都执行一次 | `0 0 0,13,18,21 * * ?` |

> #### 整合 Spring

1. 导入所需要的`jar`
2. 定义类实现 `Job` 接口 : 定义任务执行的方法
3. 在 Spring 配置文件中进行配置

> 导入所需要的`jar`

```xml
<!-- Quartz -->
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.3.0</version>
</dependency>

<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz-jobs</artifactId>
    <version>2.2.0</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>4.3.15.RELEASE</version>
</dependency>
```

> 定义类实现 `Job` 接口 : 定义任务执行的方法

```java
public class RemindJob implements Job {

	public void execute(JobExecutionContext context) throws JobExecutionException {
		System.out.println("execute(" + LocalDateTime.now() + ")");
	}

}
```

> 在 Spring 配置文件中进行配置

```xml
<!-- 配置任务 -->
 <bean id="jobDetail" class="org.quartz.impl.JobDetailImpl">
     <property name="name" value="remindJob"></property>
     <property name="group" value="group"></property>
     <property name="jobClass" value="com.job.RemindJob"></property>
</bean>
```

```xml
<!-- 配置触发器 -->
<bean name="trigger"	class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
    <!-- 配置JobDetail -->
    <property name="jobDetail" ref="jobDetail"></property>
    <!-- 配置Cron表达式 -->
    <property name="cronExpression" value="*/3 * * * * ?"></property>
</bean>
```

```xml
<!-- 配置:调度因子 -->
<bean id="schedulerBean" class="org.springframework.scheduling.quartz.SchedulerFactoryBean" lazy-init="false">
    <property name="triggers">
        <list>
            <ref bean="trigger"/>                          
        </list>        
    </property>    
</bean>
```

> 方式二

1. 编写执行方法

```java
public class Remind {
	
	public void run () {
		System.out.println("run(" + LocalDateTime.now() + ")");
	}
	
}
```

2. 在 Spring 配置文件中进行配置

```xml
<bean id="remind" class="com.znsd.spring.time.Remind"></bean>

<bean name="remindJob"
	class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
	<!-- 配置要调用的Bean实例 -->
	<property name="targetObject" ref="remind" />
	<!-- 配置要调用的方法 -->
	<property name="targetMethod" value="run" />
</bean>
	
<!-- 定义触发时间 -->
<bean id="remindTrigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
	<property name="jobDetail" ref="remindJob" />
    <!-- cron表达式 -->
    <property name="cronExpression" value="*/5 * * * * ?" />
</bean>

<!-- 配置:调度因子 -->
<bean id="schedulerBean"
	class="org.springframework.scheduling.quartz.SchedulerFactoryBean"
	lazy-init="false">
	<property name="triggers">
		<list>
			<ref bean="remindTrigger" />
		</list>
	</property>
</bean>
```

