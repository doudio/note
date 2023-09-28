> ## Docker 部署 SpringBoot 

* #### 在 DockerHub 中拉取 java8

```shell
docker pull java:8u111
```

* #### 创建 SpringBoot 项目

  * 在 https://start.spring.io 中创建项目
  * 配置好 logback-spring.xml 系统日志
  * 编写好 Controller 并打包项目

```java
@RestController
public class DemoController {
    @GetMapping("run")
    public String run() {
        long currentTimeMillis = System.currentTimeMillis();
        log.info("currentTimeMillis:{}", currentTimeMillis);
        System.out.println("currentTimeMillis:" + currentTimeMillis);
        return "success:" + currentTimeMillis;
    }
}
```

* #### 启动 容器并运行 SpringBoot

```shell
docker run -d -p 8080:8080 -v /usr/local/app/demo.jar:/home/service/java/demo/demo.jar -v /usr/local/app/demo-log:/logs --name demo java:8u111 java -jar /home/service/java/demo/demo.jar

# 解释
docker
	run # 运行容器
	-d  # 在后台运行容器并打印容器ID
	-p <local-port>:<docker-port> # 将本地的端口映射到docker中
	-v  # 映射目录
		# 将本地的jar包映射到容器内部
		-v /usr/local/app/demo.jar:/home/service/java/demo/demo.jar
		# 将容器内部的logs映射到本地
		-v /usr/local/app/demo-log:/logs
	--name <docker-image-name> # 指定容器名
	<images>:<tags> # 指定镜像与版本
	<command> # 执行指令
```

* #### 备用指令

```shell
# 进入容器内部
docker exec -it 987cbb100820 /bin/bash
# 显示所有容器（默认显示为正在运行的容器）
docker ps -a
# stop停止所有容器
sudo docker stop $(sudo docker ps -a -q)
# remove删除所有容器
sudo docker  rm $(sudo docker ps -a -q)
```

