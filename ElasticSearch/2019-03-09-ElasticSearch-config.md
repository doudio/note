> ## ElasticSearch config

> `/etc/elasticsearch/elasticsearch.yml`

```shell
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
#cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
#node.name: node-1
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
#path.data: /path/to/data
#
# Path to log files:
#
#path.logs: /path/to/logs
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
#network.host: 192.168.0.1
#
# Set a custom port for HTTP:
#
#http.port: 9200
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when new node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
#discovery.zen.ping.unicast.hosts: ["host1", "host2"]
#
# Prevent the "split brain" by configuring the majority of nodes (total number of master-eligible nodes / 2 + 1):
#
#discovery.zen.minimum_master_nodes: 3
#
# For more information, consult the zen discovery module documentation.
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#
#gateway.recover_after_nodes: 3
#
# For more information, consult the gateway module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
#action.destructive_requires_name: true
```

>  如果在重启的时候，Elasticsearch 没有起来，查看日志文件：cat /var/log/elasticsearch/elasticsearch.log 发现这么一段话：

```shell
[ERROR][o.e.b.Bootstrap          ] [avU0nZX] node validation exception
bootstrap checks failed
max number of threads [1024] for user [elasticsearch] is too low, increase to at least [2048]
```

> 那么你需要修改 /etc/security/limits.conf。添加如下配置，修改完成后续重启linux

```shell
elasticsearch   soft  nproc 2048
elasticsearch   hard  nproc 4096
```

>  如果在重启的时候报错：

```shell
system call filters failed to install; check the logs and fix your configuration or disable system call filters at your own risk
```

> 修改配置/etc/elasticsearch/elasticsearch.yml，添加如下配置

```shell
bootstrap.system_call_filter: false
bootstrap.memory_lock: false
```

> 为了支持head插件能够顺畅的连接到es服务，在/etc/elasticsearch/elasticsearch.yml加上如下配置

```shell
# 是否支持跨域
http.cors.enabled: true
#表示支持所有域名
http.cors.allow-origin: "*"
```

