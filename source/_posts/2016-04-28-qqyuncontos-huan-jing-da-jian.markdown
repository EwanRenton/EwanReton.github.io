---
layout: post
title: "腾讯云服务器Centos6.5 LNMP环境搭建"
date: 2016-04-28 01:01:35 +0800
comments: true
categories: [Nginx]
---
>今天开始搭建前几天买的腾讯云服务器的LNMP环境
<!--more-->
#一、通过putty.exe连接到云服务器
	下载并打开这个软件后只需要输入云服务器的外网IP其他默认即可
	然后用你的用户名和密码登录云服务器。
#二、CentOS环境下通过YUM安装软件
	1. 切换到root权限
	2. yum install [nginx][php][php-fpm][mariadb][mariadb-server][mysql][mysql-server] 或者yum -y mysql mysql-server nginx php php-fpm
	3. 系统会自动搜索相关的软件包和依赖关系，并且在界面中提示用户确认搜索到的软件包是否合适，如下图所示：
![sd](http://qzonestyle.gtimg.cn/qzone/vas/opensns/res/img/Yum_2.png)

	4. 输入“Y”确认后，开始安装软件，安装完成后会提示“Complete”.
	5. 软件安装完成后，可通过命令 rpm -ql 查看软件包具体的安装目录。以查看nginx的安装目录为例：
	#rpm -ql nginx
	6.可通过命令 rpm -q 查看软件包的版本信息。以查看nginx的版本为例：
	#rpm -q nginx

![sd](http://qzonestyle.gtimg.cn/qzone/vas/opensns/res/img/YaST_6.png)
![sd](http://qzonestyle.gtimg.cn/qzone/vas/opensns/res/img/YaST_5.png)
#三、CentOS环境下通过YUM安装软件

##1. 配置nginx
	1. 启动nginx服务。
		命令如下：
		[root@VM_185_51 /]# service nginx restart

	2. 测试nginx服务是否正常运行。
	如果最后显示：`index.html' saved ，说明nginx服务正常。
	[root@VM_185_51 /]# wget http://127.0.0.1
	--2013-02-20 17:07:26-- http://127.0.0.1/
	Connecting to 127.0.0.1:80... connected.
	HTTP request sent, awaiting response... 200 OK
	Length: 151 [text/html]
	Saving to: `index.html'
	100%[==========================================================================================>] 151 --.-K/s in 0s 
	2013-02-20 17:07:26 (37.9 MB/s) - `index.html' saved [151/151]

	3. 在浏览器中，通过CentOS云服务器公网IP查看nginx服务是否正常运行。
	如果显示如下，说明nginx安装配置成功。
	
![sd](http://qzonestyle.gtimg.cn/qzone/vas/opensns/res/img/Q_lnmp_1.png)
##2. 配置PHP
	1. 启动php-fpm。
		[root@VM_185_51 /]# service php-fpm start

	2. 修改php-fpm和nginx的配置，实现nginx和php联动。
		（1）查看php-fpm默认配置。
		[root@VM_185_51 /]# cat /etc/php-fpm.d/www.conf |grep -i 'listen ='
		listen = 127.0.0.1:9000
		（2）php-fpm的默认配置的监听端口为9000，现在只用修改配置，将php解析的请求转发到127.0.0.0:9000处理即可。
		修改nginx配置，修改命令如下：
	[root@VM_185_51 /]# vim /etc/nginx/conf.d/default.conf
	修改完成后实际配置如下，红色文字为修改过的部分：
	server {
 	 listen       80;
 	 root   /usr/share/nginx/html;
	 server_name  localhost;
 	 #charset koi8-r;
 	 #access_log  /var/log/nginx/log/host.access.log  main;
 	 location / {
 	     index  index.html index.htm;
	  }
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
	  location ~ \.php$ {
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index   index.php;
      fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
      include        fastcgi_params;
 	 }
 	# deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
 	#
 	#location ~ /\.ht {
 	#    deny  all;
 	#}
	}
	修改完成后，按“Esc”键，输入“:wq”，保存文件并返回。
	通过下面的命令，查看配置是否正确：
	[root@VM_185_51 /]# cat /etc/nginx/conf.d/default.conf
	3. 配置生效
		重启nginx，使配置生效。
		命令如下：
		[root@VM_185_51 /]# service nginx restart
		Stopping nginx: [ OK ]
		Starting nginx: [ OK ]
	4. 环境配置验证
		1. 在web目录下创建index.php。
		命令如下：
		[root@VM_185_51 /]# vim /usr/share/nginx/html/index.php
		index.php文件的内容如下：
		<?php
		echo "<title>Test Page</title>";
		echo "hello world";
		?>
	2. 在浏览器中，通过CentOS云服务器公网IP查看环境配置是否成功。
	如果页面可以显示“hello world”，说明配置成功。

	
![sd](http://qzonestyle.gtimg.cn/qzone/vas/opensns/res/img/Q_lnmp_2.png)	 

#四、Windows机器通过WinSCP上传文件

>WinSCP是一个Windows环境下使用SSH的开源图形化SFTP客户端，同时支持SCP协议。它的主要功能就是在本地与远程计算机间安全的复制文件。与使用FTP上传代码相比，通过WinSCP可以直接使用服务器账户密码访问服务器，无需在服务器端做任何配置。下载地址：[官方下载](http://winscp.net/eng/docs/lang:chs) [太平洋下载中心下载](http://dl.pconline.com.cn/html_2/1/86/id=7244&pn=0.html)

安装完成后启动WinSCP，界面如下。按图示填写信息并登录。
![sd](http://mccdn.qcloud.com/img56b024e2768ad.png)

>字段填写说明：


* 协议：SFTP或者SCP均可
* 主机名：云服务器的公网IP（登录云服务器控制台可查看云服务器的公网IP）
* 用户名：云服务器的系统用户名（SUSE/CentOS/Debian：root，Windows：Administrator，Ubuntu：ubuntu）
* 密码：云服务器的用户名对应的密码
* 端口：默认22

 信息填写完毕之后点击登录，界面如下:
	
![sd](http://mccdn.qcloud.com/img56b0272d4ed3a.png)

登录成功之后，鼠标选中本地文件，拖拽到右侧的远程站点，即可将文件上传到Linux云服务器
![sd](http://mccdn.qcloud.com/img56b027728e1ec.png)