> ## 01 Docker Install.md

> #### CentOS6 `Install`

> 安装`Docker`依赖库

```shell
yum install -y epel-release
```

> 安装`Docker`

```shell
yum install -y docker-io
```

> 安装成功后查看配置文件

```shell
cat /etc/sysconfig/docker
```

> 启动`Docker`服务

```shell
service docker start
```

> 查询`Docker`安装的版本

```shell
docker version
```

> #### CentOS7 `Install`

> 参照[官方文档](https://docs.docker.com/install/linux/docker-ce/centos/)

