> ## Nginx 给链接添加权限认证

> 安装 htpasswd 工具

```shell
# centos
yum install httpd-tools

# ubuntu
apt install apache2-utils
```

> 通过 htpasswd 给admin用户生成密匙

```shell
htpasswd -c /etc/nginx/.urlpass admin
# 生成隐藏密匙文件
```

> 在nginx配置文件中添加监听

```nginx
location / {
    # 设置 auth
    auth_basic "login auth";
    auth_basic_user_file /etc/nginx/.urlpass;

    # 转发到 kibana
    proxy_pass http://192.168.10.88:5601;
    proxy_redirect off;
}
```

