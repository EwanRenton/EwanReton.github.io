---
layout: post
title: "Nginx虚拟主机配置"
date: 2016-05-16 23:36:00 +0800
comments: true
categories: [nginx]
---
>今天闲下来决定把腾讯云服务器上的网站弄成虚拟主机。毕竟通过 /xx/xx访问也不成样子 0.0
<!--more-->
#增加虚拟主机
1.  进入 ```/etc/nginx/conf.d``` 目录, 创建虚拟主机配置文件 demo.neoease.com.conf ({域名}.conf) 这个目录每个人都是不同的，进到你的配置目录就好了
2.   打开配置文件, 添加服务如下:

<pre class="prettyprint linenums">
server {
	listen       80;
	server_name demo.neoease.com;
	index index.html index.htm index.php;
	root  /var/www/demo_neoease_com;
 
	log_format demo.neoease.com '$remote_addr - $remote_user [$time_local] $request'
	'$status $body_bytes_sent $http_referer '
	'$http_user_agent $http_x_forwarded_for';
	access_log  /var/log/demo.neoease.com.log demo.neoease.com;
}
</pre>
3. 打开 Nginx 配置文件 /usr/local/nginx/conf/nginx.conf, 在 http 范围引入虚拟主机配置文件如下:

<pre class="prettyprint">
include vhost/*.conf;
这个一般来说都是有的。
</pre> 
#让 Nginx 虚拟主机支持 PHP
在前面第 2 步的虚拟主机服务对应的目录加入对 PHP 的支持, 这里使用的是 FastCGI, 修改如下.
<pre class="prettyprint linenums">
server {
        listen       80;
        server_name class.ewanreton.cn;
        index index.html index.htm index.php;
        root  /usr/share/nginx/html/class;

            location ~ \.php$ {
        root           /usr/share/nginx/html/class;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

        log_format class.ewanreton.cn '$remote_addr - $remote_user [$time_local] $request'
        '$status $body_bytes_sent $http_referer '
        '$http_user_agent $http_x_forwarded_for';
        access_log  /var/log/class.ewanreton.cn.log class.ewanreton.cn;
}
这个是我腾讯云服务器上的版本。中间配置PHP支持与原博文有所不一样。
</pre> 

重启 Nginx 服务, 执行以下语句.
<pre class="prettyprint linenums">
service nginx restart
</pre>

[参考博客](http://www.neoease.com/nginx-virtual-host/)