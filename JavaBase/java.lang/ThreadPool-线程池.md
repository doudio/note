> ### 线程池案例

* SimpleThread 简单实现

```java
import lombok.extern.slf4j.Slf4j;
import java.util.concurrent.Callable;

@Slf4j
public class SimpleThread implements Callable<String> {

    private long startIndex;
    private long endIndex;

    public SimpleThread(long startIndex, long endIndex) {
        this.startIndex = startIndex;
        this.endIndex = endIndex;
    }

    @Override
    public String call() throws Exception {
        for (long i = startIndex; i <= endIndex; i++) {
            Thread.sleep(100);
            System.out.println(Thread.currentThread().getName() + "::" + i);
        }
        return Thread.currentThread().getName() + ":<" + startIndex + ":" + endIndex + ">:(success)";
    }

}
```

* ThreadPollConfig 线程池相关配置

```java
public class ThreadPollConfig {

    /**
     * 核心线程池大小
     */
    public static final int CORE_POOL_SIZE = 5;
    /**
     * 最大线程池大小
     */
    public static final int MAX_POOL_SIZE = 10;
    /**
     * 阻塞任务队列大小
     */
    public static final int QUEUE_CAPACITY = 100;
    /**
     * 空闲线程存活时间
     */
    public static final Long KEEP_ALIVE_TIME = 1L;

}
```

* main 运行

```java
public static void main(String[] args) {
    ThreadPoolExecutor executor = new ThreadPoolExecutor(
        CORE_POOL_SIZE,
        MAX_POOL_SIZE,
        KEEP_ALIVE_TIME,
        TimeUnit.SECONDS,
        new ArrayBlockingQueue<>(QUEUE_CAPACITY),
        new ThreadPoolExecutor.CallerRunsPolicy());

    List<Future<String>> futureList = new ArrayList<>();

    Long currIndex = 100L;// 当前任务进度
    Long lastTmp = currIndex;// 最近一次任务开始位
    Long maxTask = 3000L;// 最大任务
    Long taskSize = 100L;// 每个任务的大小

    currIndex += taskSize;

    for (Long dataIdTmp = currIndex /*结束ID*/
         ; dataIdTmp <= maxTask /*如果结束ID<=最大ID了就继续*/
         ; lastTmp = dataIdTmp /*记录上一个循环的结束ID*/, dataIdTmp += taskSize /*跳转到下一个结束ID*/) {
        log.info("createTask 开始ID: {}, 结束ID: {}", lastTmp, dataIdTmp);
        Future<String> future = executor.submit(new SimpleThread(lastTmp, dataIdTmp));
        futureList.add(future);
    }

    try {
        // 遍历完成后线程池内的线程都执行完毕了
        for (Future<String> future : futureList) {
            log.info("输出每个线程的返回: {}", future.get());
        }
        log.info("线程池结束");
    } catch (Exception e) {
        log.error(e.getMessage(), e);
    } finally {
        executor.shutdown();
    }
}
```



