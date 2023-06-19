> ## 04 Docker Command.md

> ### 帮助命令

| 命令           | 描述                 |
| -------------- | -------------------- |
| docker version | 查询`docker`版本     |
| docker info    | 输出`docker`基本信息 |
| docker --help  | 输出指令帮助         |

> ### 镜像命令

> #### images :: 查询本地镜像

```shell
[root@localhost ~]# docker images
REPOSITORY	TAG		IMAGE ID		CREATED			VIRTUAL	SIZE
hello-world	latest	9f5834b25059	7 weeks ago		1.84 kB
```

> ##### 携带的参数

| 参数       | 描述             |
| ---------- | ---------------- |
| -a         | 列出本地所有镜像 |
| -q         | 显示镜像ID       |
| --digests  | 显示镜像摘要信息 |
| --no-trunc | 显示完整镜像信息 |

> ### search :: 搜索远程镜像仓库

```shell
docker search tomcat
```

> ##### 携带的参数

| 参数        | 描述                    |
| ----------- | ----------------------- |
| --no-trunc  | 显示完整的镜像描述      |
| -s          | 指定条件收藏数大于 `n`  |
| --digests   | 显示镜像摘要信息        |
| --automated | 显示automated类型的镜像 |

> ### pull :: 下载镜像

```shell
docker pull tomcat:latest
```

> ### rmi :: 删除镜像

```shell
# 删除莫个镜像
docker rmi IMAGE ID

# 删除单个镜像
docker rmi -f IMAGE ID

# 删除多个镜像
docker rmi -f IMAGE ID IMAGE ID

# 删除全部镜像
docker rmi -f $(docker images -qa)
```

