---
title: Nginx记录
date: 2018-11-13
category: 
- devops
---
## 前言
Nginx是什么？是一个高性能的HTTP和反向代理服务，也是一个IMAP/POP3/SMTP服务。
为什么用Nginx？以下图中对主流web服务进行了比较，一图胜前言：
<img src="./img/web服务器对比图.png">

## 功能
### 正向代理
特点：客户端非常明确要访问的服务器地址；服务器只清楚请求来自哪个代理服务器，而不清楚来自哪个具体的客户端；正向代理模式屏蔽或者隐藏了真实客户端信息。
Eg：翻墙的服务器、WEB项目常见的Node中间层都属于正向代理。

### 反向代理
特点：多个客户端给服务器发送的请求，Nginx服务器接收到之后，按照一定的规则分发给了后端的业务处理服务器进行处理。请求的来源也就是客户端是明确的，但是请求具体由哪台服务器处理的并不明确了。
Eg: 访问量爆表了，需要服务器集群分布式部署。

### 负载均衡
负载量：客户端发送的、Nginx反向代理服务器接收到的请求数量。
均衡规则：反向代理按照一定的规则将请求分发到不同的服务器上进行处理。
硬件负载：如F5负载。
软件负载：利用软件层面技术结合主机硬件实现的消息队列分发机制。
Nginx支持的负载均衡调度算法：
- weight轮询：
- ip_hash：
- fair：
- url_hash：

## 配置

内置全局变量：

| 变量名             | 功能                                                         |
| :----------------- | :----------------------------------------------------------- |
| `$host`            | 请求信息中的 `Host`，如果请求中没有 `Host`行，则等于设置的服务器名 |
| `$request_method`  | 客户端请求类型，如 `GET`、 `POST`                            |
| `$remote_addr`     | 客户端的 `IP`地址                                            |
| `$args`            | 请求中的参数                                                 |
| `$content_length`  | 请求头中的 `Content-length`字段                              |
| `$http_user_agent` | 客户端agent信息                                              |
| `$http_cookie`     | 客户端cookie信息                                             |
| `$remote_addr`     | 客户端的IP地址                                               |
| `$remote_port`     | 客户端的端口                                                 |
| `$server_protocol` | 请求使用的协议，如 `HTTP/1.0`、·HTTP/1.1`                    |
| `$server_addr`     | 服务器地址                                                   |
| `$server_name`     | 服务器名称                                                   |
| `$server_port`     | 服务器的端口号                                               |

### main模块
ginx的全局配置，对全局生效。
- user用来指定nginx worker进程运行用户以及用户组，默认nobody账号运行
- worker_processes指定nginx要开启的子进程数量，运行过程中监控每个进程消耗内存(一般几M~几十M不等)根据实际情况进行调整，通常数量是CPU内核数量的整数倍
- error_log定义错误日志文件的位置及输出级别【debug / info / notice / warn / error / crit】
- pid用来指定进程id的存储文件的位置
- worker_rlimit_nofile用于指定一个进程可以打开最多文件数量的描述
```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;
```
### events模块
用于nginx工作模式的配置，影响nginx服务器或与用户的网络连接。
- worker_connections 指定最大可以同时接收的连接数量，这里一定要注意，最大连接数量 是和worker processes共同决定的。
- multi_accept 配置指定nginx在收到一个新连接通知后尽可能多的接受更多的连接
- use epoll 配置指定了线程轮询的方法，如果是linux2.6+，使用epoll，如果是BSD如Mac请使用Kqueue 

```
event {
    worker_connections 1024;
    multi_accept on;
    use epoll;
}
```
### http模块
用于进行http协议信息的一些配置，可以嵌套多个server；配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。
- 基础配置
- 日志配置
- SSL证书加密
- 压缩配置
- 文件缓存配置 
```
http {
    # 基础配置
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;
    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # SSL证书配置
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    # 日志配置
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
		error_page 500 501 502 503 504 506 /50x.html
			location / {
				root /root/static/html;
			}

    # Gzip 压缩配置
    gzip on;
    gzip_disable "msie6";
    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript
 text/xml application/xml application/xml+rss text/javascript;

    # 虚拟主机配置
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
    
    server{...}
    server{...}
    
    location{...}
    location{...}
    
    upstream {...}
```
#### server模块
配置虚拟主机的相关参数，一个http中可以有多个server。
- server：一个虚拟主机的配置，一个http中可以配置多个server
- server_name：用力啊指定ip地址或者域名，多个配置之间用空格分隔
- root：表示整个server虚拟主机内的根目录，所有当前主机中web项目的根目录
- index：用户访问web网站时的全局首页
- charset：用于设置www/路径中配置的网页的默认编码格式
- access_log：用于指定该虚拟主机服务器中的访问记录日志存放路径
- error_log：用于指定该虚拟主机服务器中访问错误日志的存放路径
```
server {
    listen        80;
    server_name localhost    192.168.1.100;
    root        /nginx/www;
    index        index.php index.html index.html;
    charset        utf-8;
    access_log    logs/access.log;
    error_log    logs/error.log;
    ......
}
```
#### location模块
配置请求的路由，以及各种页面的处理情况。
- location /：表示匹配访问根目录
- root：用于指定访问根目录时，访问虚拟主机的web目录
- index：在不指定访问具体资源时，默认展示的资源文件列表
```
# 基本配置
location / {
	root   html;
    index  index.html index.htm;
}
# 反向代理配置
location / {
    proxy_pass http://localhost:8888;
    proxy_set_header X-real-ip $remote_addr;
    proxy_set_header Host $http_host;
}
# uwsgi配置
location / {
    include uwsgi_params;
    uwsgi_pass localhost:8888
}
```
#### upstream模块
用于进行负载均衡的配置，配置后端服务器具体地址。
- ip_hash：指定请求调度算法，默认是weight权重轮询调度，可以指定
- server host:port：分发服务器的列表配置
- -- down：表示该主机暂停服务
- -- max_fails：表示失败最大次数，超过失败最大次数暂停服务
- -- fail_timeout：表示如果请求受理失败，暂停指定的时间之后重新发起请求
```
upstream name {
    ip_hash;
    server 192.168.1.100:8000;
    server 192.168.1.100:8001 down;
    server 192.168.1.100:8002 max_fails=3;
    server 192.168.1.100:8003 fail_timeout=20s;
    server 192.168.1.100:8004 max_fails=3 fail_timeout=20s;
}
```
## 基本操作
### Windows下使用
```
# windows下安装还有什么好说的 :)
# 启动nginx(双击nginx.exe)
D:\tool\nginx-1.14.0\ .\nginx

# 强制停止nginx(会丢弃掉有未处理的数据)
D:\tool\nginx-1.14.0\ .\nginx -s stop

# 优雅停止nginx(会等处理完未处理的数据之后，再停止)
D:\tool\nginx-1.14.0\ .\nginx -s quit
```
### Linux下使用
```
# 安装nginx(默认ubuntu)
sudo apt-get install nginx
/usr/sbin/  ：nginx命令所在目录，
/etc/nginx/ ：nginx所有的配置文件，用于配置nginx服务器以及负载均衡等信息

# 查看nginx进程是否启动
/etc/nginx$ ps -ef|grep nginx

# 启动niginx
/etc/nginx$ sudo nginx

# 停止nginx
/etc/nginx$ nginx -s stop
/etc/nginx$ nginx -s quit
```

## 小结

