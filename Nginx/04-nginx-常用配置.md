> 配置nginx端口转发

```json
# 配置虚拟主机，基于域名、ip和端口
server {
        # 监听端口
        listen       80;
        # 监听域名
        server_name 49.233.142.59
        
        # 设置字符集
        charset utf-8;
        
        # 监听 url
        location / {
                # 代理到哪个地址
                proxy_pass http://49.233.142.59:8080;
        }
}

```

> 配置在请求头中携带ip地址

```json
location /api/ {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header REMOTE-HOST $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://localhost:8088/;
}
```

> 配置静态资源目录

```json
location /static/ {
    alias /usr/local/blog/static/;
    autoindex on;
}
```

> 注意: 在nginx配置静态资源目录中的良好习惯是:

1) 在 location / 中配置root目录;
2) 在 location /path 中配置alias虚拟目录;

> 引入外部配置

```json
#######################引入扩展配置文件##################################
include  /usr/local/nginx/conf/vhost/*.conf;
```

---

> 外部文件中的内容

```json
#user  nobody;
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;
client_max_body_size 50m;

server {
  listen       80;
  server_name  172.16.10.110;
  keepalive_timeout 70;

location / {
    root /data/html;
    try_files $uri $uri/ /index.html;
    index index.html;
}

location /proxyapi/ {
  proxy_pass http://127.0.0.1:8088;
  proxy_http_version 1.1;
  proxy_read_timeout 3600;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "Upgrade";
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Real-IP $remote_addr;
}

}
```