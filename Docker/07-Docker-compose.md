> ### docker-compose

* docker-compose :: [官方文档](https://docs.docker.com/compose)
* https://docs.docker.com/compose

> 安装docker-compose

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

* echo $(uname -s)-$(uname -m) 获取系统信息 Linux-x86_64

> 赋予可执行权限

```shell
sudo chmod +x /usr/local/bin/docker-compose
```

> 参看 docker-compose 版本

```shell
docker-compose --version
docker-compose version 1.25.5, build 1110ad01
```

> 在当前目录下创建默认配置 `docker-compose.yml` 文件

* docker-compose.yml 的[规范](https://docs.docker.com/compose/compose-file/) :: https://docs.docker.com/compose/compose-file/

```yaml
# docker-compose 的版本号
version: "2"
services:

  # 服务名可自定义
  my-service1:
    # 指定容器名称
    container_name: demo
    # 重启机制
    restart: always
    image: java:8u111
    volumes:
      - /usr/local/docker/demo.jar:/home/service/java/demo/demo.jar
      - /usr/local/docker/demo-log:/logs
    ports:
      - "8098:8080"
    environment:
      - TZ="Asia/Shanghai"
    command: [
      'java',
      '-Xmx1024m',
      '-Xms1024m',
      '-jar',
      '/home/service/java/demo/demo.jar'
    ]
```

> 如果时区还是有问题那么添加配置

```
volumes:
  - /usr/local/docker/demo.jar:/home/service/java/demo/demo.jar
  - /usr/local/docker/demo-log:/logs
  - /etc/localtime:/etc/localtime
  - /etc/timezone/timezone:/etc/timezone:ro
```

> 启动 docker-compose.yml `-d` 后台启动

```shell
docker-compose up -d
# 如果镜像已经存在需要重新构建
# 重新构建自定义镜像
docker-compose build
# 运行前，重新构建
docker-compose up -d --build
```

> docker-compose.yml 常用指令

```shell
# 关闭并删除容器
docker-compose down
# 启动|关闭|重启 已经存在的由docker-compose维护的容器
docker-compose start|stop|restart
# 查看由 docker-compose 管理的容器
docker-compose ps
# 查看日志
docker-compose logs -f
```

> docker-compose配置Dokcerfile使用

* docker-compose.yml

```yaml
version: '3.1'
services:
    myserver: 
        restart: always
        build: #指定自定义镜像
            context: ../ #指定dockerfile文件的所在路径
            dockerfile: Dockerfile #指定Dockerfile文件名称
        image: myserver:1.0.1
        container_name: myserver
        ports: 
            - 8080:8080
        environment: 
            TZ: Asia/Shanghai
```

* Dockerfile文件

```dockerfile
FROM java:8
MAINTAINER <作者>
RUN echo 'Asia/Shanghai' > /etc/timezone
COPY api-server-0.0.1-SNAPSHOT.jar /
ENTRYPOINT ["java","-jar","api-server-0.0.1-SNAPSHOT.jar"]
```
