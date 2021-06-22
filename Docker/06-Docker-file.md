> DockerFile

* dockerFile官方文档地址: https://docs.docker.com/engine/reference/builder/

* 编写 Dockerfile
  * $ `vim Dockerfile`

```dockerfile
FROM java:8u111
MAINTAINER xg
ADD demo-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

* 编译 Dockerfile 成为 images
  * $ `docker build -t springboot-demo:1.0 .`

> 导入导出镜像

```shell
# 保存镜像
docker save bcb0e189df29 > demo.tar

# 导入镜像
docker import <demo.tar>
# 给镜像打 tag
docker tag <images-id> springboot-demo:1.0
```
