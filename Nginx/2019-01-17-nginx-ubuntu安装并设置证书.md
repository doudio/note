> nginx-ubuntu安装并配置证书.md

* nginx 官网下载地址 http://nginx.org/en/download.html

```shell
cd /usr/local/apps
wget http://nginx.org/download/nginx-1.17.8.tar.gz
tar zxf nginx-1.2.8.tar.gz

# 安装nginx所需库
sudo apt-get update
sudo apt-get install libpcre3 libpcre3-dev zlib1g-dev

## https 所需库
sudo apt-get install openssl
sudo apt-get install libssl-dev

cd nginx-1.17.8

./configure --with-http_ssl_module

make
make install
```

| 名称         | 目录                             |
| ------------ | -------------------------------- |
| 配置文件     | /usr/local/nginx/conf/nginx.conf |
| 程序文件     | /usr/local/nginx/sbin/nginx      |
| 日志         | /var/log/nginx                   |
| 默认虚拟主机 | /var/www/                        |

> 给nginx配置环境

```shell
vim /etc/profile

##
export PATH=<nginx-install-dir:default:/usr/local/nginx/sbin>:$PATH
##
```

> 开始配置 ssl 证书

> 在域名备案后可以去相关的云平台下载ssl证书

```shell
.
├── Apache
│   ├── 1_root_bundle.crt
│   ├── 2_www.XXXX.com.crt
│   └── 3_www.XXXX.com.key
├── IIS
│   ├── keystorePass.txt
│   └── www.XXXX.com.pfx
├── Nginx
│   ├── 1_www.XXXX.com_bundle.crt
│   └── 2_www.XXXX.com.key
├── Tomcat
│   ├── keystorePass.txt
│   └── www.XXXX.com.jks
├── www.XXXX.com.csr
└── www.XXXX.com.zip
```

> 修改 nginx.conf 部分配置

* 配置 http 转成 https

```json
server {
        listen       80;
        server_name  www.XXXX.com;

        rewrite ^(.*)$  https://$host$1 permanent;
}
```

* 配置 ssl 证书 给原有注释的 # HTTPS server 拷贝一份

```json
# HTTPS server
#
server {
    listen       443 ssl;
    server_name  www.XXXX.com;

    ssl_certificate      /usr/local/nginx/ssl/Nginx/1_www.XXXX.com_bundle.crt;
    ssl_certificate_key  /usr/local/nginx/ssl/Nginx/2_www.XXXX.com.key;

    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;

    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers  on;

    location / {
        root   html;
        index  index.html index.htm;
    }

}
```

* 证书到这里配置完成了然后给 nginx 启动就好了

