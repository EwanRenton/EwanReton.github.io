---
layout: post
title: "Nginx下PHP环境搭建1"
date: 2016-04-26 22:53:05 +0800
comments: true
categories: [Nginx,PHP]
---
>Nginx下PHP环境搭建1
<!--more-->
#一、需要准备的包
* libiconv-1.7.tar.gz
* libmcrypt-2.5.7.tar.gz
* mcrypt-2.6.8.tar.gz
* mhash-0.9.3.tar.gz
* php-5.5.34.tar.bz2

#二、具体安装
---
1. libiconv
	
	1. tar -zvxf libiconv-1.7.tar.gz
	2. cd libiconv-1.7
	2. ./configure --prefix=/usr/local
	3. make
	4. make install

2. libmcrypt
	1. tar -zvxf libmcrypt-2.5.7.tar.gz
	2. cd libmcrypt-2.5.7
	2. ./configure
	3. make
	4. make install
	5. /sbin/ldconfig   //共享链接库
	6. ./configure --enable-ltdl-install //内键指令
	7. make
	8. make install
3. mhash
	1. tar -zvxf mhash-0.9.3.tar.gz
	2. cd mhash-0.9.3
	3. ./configure
	4. make 
	5. make install
	6. ln -s /usr/local/lib/libmcrypt.la /usr/lib/ibmcrypt.la
	7. ln -s /usr/local/lib/libmcrypt.la.so.4 /usr/lib/libmcrypt.la.so.4
	8. ln -s /usr/local/lib/libmcrypt.la.so.4.4.8 /usr/lib/libmcrypt.la.so.4.4.8
	9. ln -s /usr/local/lib/libmhash.a /usr/lib/libmhash.a
	10. ln -s /usr/local/lib/libmhash.la /usr/lib/libmhash.la
	11. ln -s /usr/local/lib/libmhash.so /usr/lib/libmhash.so
	12. ln -s /usr/local/lib/libmhash.so.2 /usr/lib/libmhash.so.2
	13. ln -s /usr/local/lib/libmhash.so.2.0.1 /usr/lib/libmhash.so.2.0.1

4. mcrypt
	1. tar -zxvf mcrypt-2.6.8.tar.gz
	2. cd mcrypt-2.6.8
	2. /sbin/ldconfig
	3. ./configure
	4. 若报错则找不到LIBMCRYPT则
		1. export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
		2. ./configure -with-libmcryp-prefix=/usr/local
	5. make
		1. 报错打开报错的文件 vi /usr/local/include/mutils.h
		2. 找到报错的那行看是哪个文件引入失败(mhash_config.h)
		3. find / -name mhash_config.h 在系统中找到该文件
		4. cp /home/Ewan/下载/mhash-0.9.3/mhash_config.h /usr/local/include 拷贝到include文件下
		5. make
	6. make install

5. php-5.5.34.tar.bz2
	1. tar -jxvf php-5.5.34.tar.bz2
	2. cd php-5.5.34
	3. ./configure --prefix=/usr/local/php --enable-fastcgi --enable-fpm
		1. 报错 yum install libxml2 libxml2-devel
		2. ./configure --prefix=/usr/local/php --enable-fastcgi --enable-fpm
	4. make ZEND_EXTRA_LIBS='-liconv'
	5. make install
		1. 报错 vi /etc/ld.so.conf
		2. 输入 /usr/local/lib
		3. make install
		4. 如果还是出错 /sbin/ldconfig -v
		5. make install
		6. cp php.ini-dist /usr/local/php/etc/php.ini

6. mysql (5.5版本以下)
	1. groupadd mysql //添加组
	2. useradd mysql -g mysql //添加用户
	3. tar -zxvf mysql-x.x.x
	4. cd mysql-x.x.x
	5. ./configure --prefix=/usr/local/mysql --with-mysqld-ldflags=-all-static --with-client-ldflags=-all-static --with-readline --with-ssl
	6. /usr/local/mysql/bin/mysql_install_db --user//初始化数据库
	7. cp ./support-files/mysql.server /etc/init.d/mysql //复制启动项到系统中
	8. cp ./support-files/my-medium.cnf /etc/my.cnf 
	9. chmod 755 /etc/init.d/mysql(755 分别代表文件拥有者，同组者，其他人的权限 rwx 7为111 可读可写可执行)
	10. cd /usr/local/mysql
	11. chown -R mysql . //将拥有者更改为mysql用户
	12. chgrp -R mysql . //归到mysql组
	13. /usr/local/mysql/bin/mysqladmin -u root password 'abc123' //设置数据库密码
	14. 报错 不能连接到local mysql
	15. ps -ef|grep mysql //找到mysql对应的进程号
	16. kill -9 xxxx //杀死进程
	17. /usr/local/mysql/bin/mysql_install_db --user//初始化数据库
	18. service mysql start //启动MYSQL
	19. /usr/local/mysql/bin/mysqladmin -u root password 'abc123' //设置数据库密码
	20. /usr/local/mysql/bin/mysql -u root -p //测试输入的密码
	21. 进入到mysql 后可以\q退出
7. 安装mysql（5.5版本）
	 [下载Mysql5.5.4](http://www.mysql.com/)

![1](http://shp.qpic.cn/txdiscuz_pic/0/_bbs_qcloud_com_forum_201503_10_171832lw4gbxwhq9z9vmho.png/0)

![1](http://shp.qpic.cn/txdiscuz_pic/0/_bbs_qcloud_com_forum_201503_10_171836mubv0eb5010ewbjv.png/0)


	1. tar zxvf mysql-5.5.40.tar.gz
	2. cd mysql-5.5.4
	3. yum install ncurses-devel cmake bison-devel libaio-devel gcc-c++ bison -y 
		*  mysql5.5 版本之后，预编译的 configure命令改为了 cmake, 需要手动安装一下 cmake命令
	5.  cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/usr/local/mysql/data -DSYSCONFDIR=/etc -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci-- Library mysqlclient depends on OSLIBS -lpthread;m;rt;dl-- Configuring done-- Generating done-- Build files have been written to: /root/mysql-5.5.40 ————预编译无报错，成功。
	*  make 
		*  [100%] Building CXX object mysql-test/lib/My/SafeProcess/CMakeFiles/my_safe_process.dir/safe_process.cc.oLinking CXX executable my_safe_process[100%] Built target my_safe_process————无报错，make正常。
	*   make install -- Installing: /usr/local/mysql/man/man1/mysql_fix_extensions.1 -- Installing: /usr/local/mysql/man/man1/mysqldumpslow.1 -- Installing: /usr/local/mysql/man/man1/mysql_secure_installation.1 -- Installing: /usr/local/mysql/man/man8/mysqld.8 ————无报错，make install正常。
	*    groupadd mysql ————创建mysql组
	*     useradd -g mysql mysql ————在mysql组中创建mysql用户
	* cp /usr/local/mysql/support-files/my-medium.cnf /etc/my.cnf ————默认没有mysql的配置文件，将源码包中的配置文件拷贝到etc下的配置文件中。
	*  /usr/local/mysql/scripts/mysql_install_db --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data ————初始化数据库，指定用户为mysql，家目录为/usr/local/mysql，数据库目录为/usr/local/mysql/data
	* chown -R root /usr/local/mysql/ ————改变目录的所有者
	* chown -R mysql /usr/local/mysql/data ————改变目录的所有者
	* chgrp -R mysql /usr/local/mysql/ ————改变目录的所属组
	*  export PATH=$PATH:/usr/local/mysql/bin ————将/usr/local/mysql/bin下的命令放到环境变量中
	*   /usr/local/mysql/bin/mysqld_safe --user=mysql & ————启动mysql
	* mysql 
		*  Welcome to the MySQL monitor.  Commands end with ; or \g.
		     Your MySQL connection id is 1

>ps:在实际的操作过程中上面在10步初始化数据库时有可能会error这时可以考虑用


	 ps -ef|grep mysql //找到mysql对应的进程号
	 kill -9 xxxx //杀死进程
>然后执行

	 chown -R root /usr/local/mysql/ ————改变目录的所有者
	 chown -R mysql /usr/local/mysql/data ————改变目录的所有者
	 chgrp -R mysql /usr/local/mysql/ ————改变目录的所属组
	 export PATH=$PATH:/usr/local/mysql/bin ————将/usr/local/mysql/bin下的命令放到环境变量中
	 /usr/local/mysql/scripts/mysql_install_db --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data ————初始化数据库，指定用户为mysql，家目录为/usr/local/mysql，数据库目录为/usr/local/mysql/data
	 /usr/local/mysql/bin/mysqld_safe & 启动Mysql 或者
	 /usr/local/mysql/bin/mysqld_safe --user=mysql & ————启动mysql
	 在执行以下操作

##Mysql启动、停止的方法

 	/usr/local/mysql/bin/mysqld_safe --user=mysql &      启动
 	mysqladmin –uroot –pxxx  shutdown                  停止
 	tail -f /var/log/mysqld.log                        查看mysql的错误日志


###修改 mysql监听地址为127.0.0.1


	netstat -ntulp
	tcp        0      0 0.0.0.0:3306                0.0.0.0:*                   LISTEN      28253/mysqld       
	tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN      1889/sshd    
  
默认是监听的0.0.0.0

 	vi /etc/my.cnf
	添加一列
	[mysqld]
	port            = 3306
	socket          = /tmp/mysql.socK
	bind-address=127.0.0.1
	# mysqladmin -uroot -p shutdow
	# /usr/local/mysql/bin/mysqld_safe --user=mysql &
	保存并退出
	 netstat -ntulp
	tcp        0      0 127.0.0.1:3306              0.0.0.0:*                   LISTEN      29103/mysqld   
————修改成功 


##修改 mysql的root密码
	use mysql
	update user set Password=PASSWORD('新密码') where User='root' and Host='localhost';
	flush privileges;flush privileges;
	# mysql -uroot -p
	Enter password:
	mysql>
————修改新密码成功
