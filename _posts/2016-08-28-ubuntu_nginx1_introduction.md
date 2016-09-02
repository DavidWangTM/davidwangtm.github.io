---
layout:     post
title:      "Nginx 操作篇"
subtitle:   "Ubuntu 设置Nginx操作配置"
date:       2016-08-28 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Ubuntu
    - Nginx
--- 

### 重点介绍nginx配置

在上篇中，安装成功后，会有 `/etc/nginx` 目录，在 `/etc/nginx` 目录中找到 `nginx.conf`.使用 `vi nginx.conf` 打开文件。

```
user  nginx;
#操作线程数
worker_processes  1;

#引用目录地址
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

#单个线程最大连接数
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

	 #超时时间
	 client_body_timeout 12;
    client_header_timeout 12;
    keepalive_timeout 15;
    send_timeout 10;
    #gzip  on;
	
	 #我们需要配置目录
    include /etc/nginx/conf.d/*.conf;
}

```

操作 `vi /etc/nginx/conf.d/deault.conf`

```
upstream site {
    server 127.0.0.1:8080 weight=1;
    }

server {
    listen       90;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;
	#静态资源截获
    location ~* ^.+\.(ico|gif|jpg|jpeg|png|html|htm)$ {
        root         /usr/share/nginx/XXX;
        access_log   /var/log/nginx/access.log;
        expires      30d;
    }

	#静态资源截获
    location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
        root         /usr/share/nginx/XXX;
        access_log   /var/log/nginx/access.log;
        expires      24h;
    }

	
    location / {
        proxy_pass      http://site;
        proxy_set_header        Host            $host;
        proxy_set_header        X-Forwarded-For         $remote_addr;
    }

    gzip on;
    gzip_comp_level 2;
    gzip_min_length  1000;
    gzip_buffers    4 8k;
    gzip_types      text/plain application/javascript text/css text/xml application/x-httpd-php;
    output_buffers  1 32k; 
    postpone_output  1460;
          
         
    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    # 
    error_page   500 502 503 504  /50x.html;
    location = /50x.html { 
        root   /usr/share/nginx/html; 
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```

### 重点讲解下：location语法规则
`=` 开头表示精确匹配

`^~` 开头表示uri以某个常规字符串开头，理解为匹配 url路径即可。nginx不对url做编码。

`~` 开头表示区分大小写的正则匹配

`~*`  开头表示不区分大小写的正则匹配

`!~` 和 `!~*` 分别为区分大小写不匹配及不区分大小写不匹配 的正则

`/` 通用匹配，任何请求都会匹配到。

多个location配置的情况下匹配顺序为：

首先匹配 `=`，其次匹配 `^~` , 其次是按文件中顺序的正则匹配，最后是交给 `/` 通用匹配。当有匹配成功时候，停止匹配，按当前匹配规则处理请求。

### Redirect语法

```
server {
    listen 90;
    server_name localhost;
    index index.html index.php;
    root html;
    if ($http_host !~ “^star\.igrow\.cn$&quot {
        rewrite ^(.*) http://XXX.cn$1 redirect;
    }
}

#放盗链
location ~* \.(gif|jpg|swf)$ {
    valid_referers none blocked XX.cn XXX.cn;
    if ($invalid_referer) {
        rewrite ^/ http://$host/logo.png;
    }
}

#过期时间
location ~* \.(js|css|jpg|jpeg|gif|png|swf)$ {
    if (-f $request_filename) {
        expires 1h;
        break;
    }
}

#禁止访问某个目录
location ~* \.(txt|doc)${
    root /usr/share/nginx/XXX;
    deny all;
}
```

一些可用的全局变量:

```
$args $content_length $content_type $document_root
$document_uri $host $http_user_agent
$http_cookie $limit_rate $request_body_file
$request_method $remote_addr $remote_port
$remote_user $request_filename $request_uri
$query_string $scheme $server_protocol
$server_addr $server_name $server_port
$uri
```

### ReWrite语法

三、ReWrite语法

last – 基本上都用这个Flag。

break – 中止Rewirte，不在继续匹配

redirect – 返回临时重定向的HTTP状态302

permanent – 返回永久重定向的HTTP状态301

1、下面是可以用来判断的表达式：

-f和!-f用来判断是否存在文件

-d和!-d用来判断是否存在目录

-e和!-e用来判断是否存在文件或目录

-x和!-x用来判断文件是否可执行

2、下面是可以用作判断的全局变量

例：http://localhost:88/test1/test2/test.php

$host：localhost

$server_port：88

$request_uri：http://localhost:88/test1/test2/test.php

$document_uri：/test1/test2/test.php

$document_root：D:\nginx/html

$request_filename：D:\nginx/html/test1/test2/test.php
