---
layout: post
title: "Ubuntu环境搭建"
date: 2016-05-12 14:58:33 +0800
comments: true
categories: 
---
>在经历上次把Centos玩坏的经历过后，我还是回到Ubuntu吧。毕竟这个环境配置，软件安装都要简单得多0 0.
<!--more-->
重新下了一个Ubuntu 12.04.5 LTS 。

在虚拟机中装好了系统过后出现了一个问题，在登录界面输入密码登录过后一直黑屏。当时人都懵逼了，用了这么久Ubuntu从来没出过这种问题啊，重装了很多次系统都不行。百度了很多过后发现他们的情况和我的都不太一样。到是看到有一个看起来很不靠谱的回答解决了问题。

<pre class="prettyprint linenums">
	在登录时点击登录框右上角那个Ubuntu图标选择 ubuntu 2D
</pre>
5/12/2016 7:18:20 PM 
找到了上述问题的原因[传送门](http://tieba.baidu.com/p/2535671580)
<pre class="prettyprint linenums>
1.双显卡问题 
双显卡的普遍组合为intel集成到其CPU上的显卡（称为核显）+Nvidia独立显卡或AMD显卡。在linux系统下，双显卡因驱动原因不能很好协调工作，N卡和A卡会一直处于高负荷运行状态，导致电脑发热严重,或是X-org罢工，开机无画面，等常见问题。
linux下是以工具Bumlebee（大黄蜂）来调度显卡工作来解决，运行模式为：通常关闭非核卡，让核卡来负责图形工作，但可在终端输入命令“optirun xxx"用非核卡运行程序xxx。
2.安装Bumblebee
2.1 卸载N卡驱动
如果有安装N卡驱动（不管是开源还是闭源），必须先卸载；没有则跳过此步。
打开终端（ctrl+alt+t），输入下面的命令（复制也可）：
————————————复制————————————————————————
sudo apt-get remove --purge nvidia*
————————————复制————————————————————————
完成后，重启。
2.2 安装Bumblebee
打开终端（ctrl+alt+t），分次按序输入下面的命令（红字部分，复制也可）：
————————————复制————————————————————————
1. sudo add-apt-repository ppa:bumblebee/stable
2. sudo apt-get update
3. sudo apt-get install bumblebee virtualgl linux-headers-generic
————————————复制————————————————————————
每步的时间较长，请耐心等待；完成后，重启。
2.3 验证效果
打开终端（ctrl+alt+t），输入下面的的命令（红字部分，复制也可）：
————————————复制————————————————————————
lspci |grep VGA
————————————复制————————————————————————
终端返回类似如下两条信息：
A、00:02.0 VGA compatible controller: Intel Corporation 3rd Gen Core processor Graphics Controller (rev 09)
B、01:00.0 VGA compatible controller: NVIDIA Corporation GK104M [GeForce GTX 670MX] (rev ff)"
其中，A条信息关于I卡，"rev 09“表示I卡运作；B条信息关于N卡，”rev ff“表示N卡被关闭，若未被关闭，显示为“rev a1”。

3、调用N卡工作
命令“optirun xxx"可用来调用N卡运作处理程序xxx（程序名）的图形计算 ，不过图形渲染仍由I卡负责。
</pre>
抱着试一试的心态结果成功登录了。
下面是Ubuntu的环境搭建，以前都是看别人的博客，现在既然自己有了博客就整理了一下。
***
安装Gnome ```apt-get install gnome-session-fallback```

#一、安装LAMP环境
两种方式

1. 分步安装

<pre class="prettyprint linenums">
sudo apt-get update //获取最新列表
Apache:
sudo apt-get install apache2
apache2 -v //查看是否安装好
浏览器访问服务器IP
php：
sudo apt-get install php5
php5 -v
cat /etc/apache2/mod-enabled/php5.load //查看阿帕奇是否加载PHP
mysql：
sudo apt-get install mysql-server
输入root密码 //注意这里的密码不是你root用户的密码，是设置mysql-root用户的密码
cat /etc/php5/conf.d/mysql.ini
提示没有文件 说明mysql还没有被php加载 需要安装mysql扩展
sudo apt-get install php5-mysql
cat /etc/php5/conf.d/mysql.ini
extension=mysql.so //说明正确加载
</pre>
2.tasksel
<pre class="prettyprint linenums">
sudo tasksel install lamp-server
tasksel 提供多种软件套装
</pre>

***
**安装PHP常用扩展**
```php
apt-get install  php5-curl php5-gd  libcurl3 libcurl3-dev php5-curl
可以先用sudo apt-cache search php5 查看关于PHP5的组件
```
**安装mysqladmin**
```java
apt-get install phpmyadmin
ln -s /usr/share/phpmyadmin/ /var/www/pma
apt-get install php5-mcrypt libmcrypt-dev
service apache2 restart
```
**开启mysql remote access**
```java
vim /etc/mysql/my.cnf
找到bind-address  将这行注释掉 这是允许访问的白名单
在mysqladmin中添加一个用户 主机选择任意主机 输入%其他的按个人设置即可。
```

#二、SSH配置
>习惯了用Xshell，今天直接用Xshell连接新安装的ubuntu连接失败，好一会才反应过来还没安装ssh

<pre class="prettyprint linenums">
sudo apt-get install openssh-server
</pre>
其他配置就不多写了。可以参考[传送门](http://jingyan.baidu.com/article/9c69d48fb9fd7b13c8024e6b.html)

#三、 ubuntu终端su认证失败：允许su到root

>Ubuntu 安装后，root用户默认被锁定，不允许登录，也不允许“su”到 root。对于桌面用户来说，这样安全性更高一些，但对于服务器可以设置成“允许 su 到root，但不允许root用户直接登录”。为了开发时的方便，我们常希望都采用这种方式。

1.允许 su 到 root

<pre class="prettyprint linenums">
sudo passwd 
Password: <--- 输入安装时那个用户的密码 
Enter new UNIX password: <--- 新的Root用户密码 
Retype new UNIX password: <--- 重复新的Root用户密码 
passwd：已成功更新密码  
</pre>

2.允许root登录
<pre class="prettyprint linenums">
gksu /usr/sbin/gdmsetup  
</pre>
（或者使用桌面菜单：系统＞系统管理＞登录窗口） 
点“安全”选项页，选择“允许本地管理员登录”。 
注：这一步依赖上一步


3.不输入密码直接登录
	在上一步的“安全”选项页中，勾选：“启用自动登录”，“用户”选择（如：“me”）

参考[传送门](http://blog.csdn.net/ccrong/article/details/8133736)

#四、Git

Git的安装就非常简单了。
<pre class="prettyprint linenums">
sudo apt-get install git
</pre>
配置请看[这里](http://blog.ewanreton.cn/blog/2016/04/18/git-plus-octopressda-jian-ge-ren-bo-ke/)

五、Atom
>对的，还是它，这是一种信仰0 0

**Requirements**

* OS with 64-bit or 32-bit architecture
* C++ toolchain
* Git
* Node.js (0.10.x or above)
* npm v1.4.x or above (automatically bundled with Node.js)
	* ```npm -v``` to check the version.
	* ```npm config set python /usr/bin/python2 -g``` to ensure that gyp uses python2.
		* You might need to run this command as ```sudo```, depending on how you have set up npm.
* development headers for GNOME Keyring


***
**Ubuntu / Debian**

* ```sudo apt-get install build-essential git libgnome-keyring-dev fakeroot```
* Instructions for Node.js.
	* Make sure the command node is available after Node.js installation (some systems install it as nodejs).
	* Use ```which node``` to check if it is available.
	* Use ```sudo update-alternatives --install /usr/bin/node node /usr/bin/nodejs 10 to``` update it.

这里我用了其他的方法更新我的node和npm

```php
 apt-get update  
 apt-get install -y python-software-properties software-properties-common  
 add-apt-repository ppa:chris-lea/node.js  
 apt-get update  
 apt-get install nodejs 
```
这样环境就好了，就可以开始安装了。

**Debian Linux (Ubuntu)**

Currently only a 64-bit version is available.

1. Download ```atom-amd64.deb``` from the [Atom releases page.](https://github.com/atom/atom/releases/latest)
2. Run ```sudo dpkg --install atom-amd64.deb``` on the downloaded package.
3. Launch Atom using the installed ```atom``` command.


The Linux version does not currently automatically update so you will need to repeat these steps to upgrade to future releases.