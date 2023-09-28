> ## SpringBoot 多线程定时任务

> 在 SpringBoot 启动类中添加 `@EnableScheduling`

```java
@EnableScheduling
@SpringBootApplication
public class CronDemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(CronDemoApplication.class, args);
	}

}
```

> 定义类实现 `SchedulingConfigurer`

```java
@Configuration
public class ScheduleConfig implements SchedulingConfigurer {

    @Override
    public void configureTasks(ScheduledTaskRegistrar scheduledTaskRegistrar) {
    	scheduledTaskRegistrar.setScheduler(Executors.newScheduledThreadPool(20));
    }

}
```

> 定义定时器类

```java
@Slf4j
@Component
public class CronApplication {

    @Scheduled(cron = "0/1 * * * * ?")
    public void runOne() throws InterruptedException {
        log.info("runOne:start");
        Thread.sleep(1000 * 10);
        log.info("runOne:end");
    }

    @Scheduled(cron = "0/1 * * * * ?")
    public void runTwo() throws InterruptedException {
        log.info("runTwo:start");
        Thread.sleep(1000 * 5);
        log.info("runTwo:end");
    }

}
```

* 注意如果定时器没执行需要检查
* 检查springboot启动类中是否有加 `@EnableScheduling`
* 检查定时器类是否存在springboot容器中, 如果没有在类上添加 `@Component`