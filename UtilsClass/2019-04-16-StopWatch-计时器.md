> ## 定时器 Stop Watch

> Demo

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.util.StopWatch;

@Slf4j
public class StopWatchMain {

    public static void main(String[] args) throws InterruptedException {
        StopWatch sw = new StopWatch();

        sw.start("接口开始请求");

        Thread.sleep(1000);

        sw.stop();

        sw.start("处理数据");

        Thread.sleep(100);

        sw.stop();

        sw.start("写入数据库");

        Thread.sleep(100);

        sw.stop();

        log.info("阶段性耗时统计: {}", sw.prettyPrint());
        log.info("总耗时(毫秒): {}", sw.getTotalTimeMillis());
    }

}
```

> Result (ns : 纳秒,ms : 毫秒, s : 秒)

```shell
阶段性耗时统计: StopWatch '': running time = 1204345200 ns
---------------------------------------------
ns         %     Task name
---------------------------------------------
999568200  083%  接口开始请求
100111200  008%  处理数据
104665800  009%  写入数据库

总耗时(毫秒): 1204

Process finished with exit code 0
```

> 计算进度

```java
int EXEC_MAX = 3000;
for (int idCount = 0; idCount <= EXEC_MAX; idCount++) {
    System.out.println(idCount * 100 / EXEC_MAX);
}
```