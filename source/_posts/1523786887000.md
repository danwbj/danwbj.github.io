---
title: CentOS7安装配置Nginx
date: 2018-04-15 18:08:07
tags: [nginx,linux,CentOS7]
catagory: [linux]
---


#### 安装
- 安装nginx依赖包
```
yum install openssl
yum install zlib
yum install pcre
```
- Nginx依赖项
```
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```
- 安装nginx
```
yum install nginx
```
- 启动nginx
```
service start nginx
或者
systemctl start nginx
```
#### 配置
主配置文件：/etc/nginx/conf.d/nginx.conf   
在主配置的include处可以修改自己的配置文件路径
 
```js

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;
    
    #include /etc/nginx/conf.d/*.conf;  // 默认配置文件位置;
    
    include /root/nginx/conf/*.conf;  // 修改为自己的路径;
}
```

