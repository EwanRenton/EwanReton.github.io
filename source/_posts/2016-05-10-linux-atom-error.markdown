---
layout: post
title: "做死的Centos-Atom"
date: 2016-05-10 20:37:45 +0800
comments: true
categories: [Linux]
---

>昨天晚上突然冒出一个念头在我的Centos6.5的虚拟机上装Atom并且装上那个炫酷的插件来装波大逼。然后很悲催，软件没装上不说，系统也被搞坏了。
<!--more-->
#兴高采烈的去下Atom

在github上找到了Atom的[开源项目](https://github.com/atom/atom)，根据指引下载好了rpm包。

然后```sudo yum localinstall atom.x86_64.rpm``` 

不出意外出现了问题。

当时忘了截图，不过我大概都还记得出了哪些问题。

>这里有个极度懵逼的事情

	我最开始在Centos中下Atom由于网络不好，时断时续就没管，后来切过去看。
	咦~为什么这么快就下好了，开心的安装。恩，问题来了 libnode.so 找不到。
	恩，有问题嘛很正常。度娘之。

	度娘好像并不能告诉我什么，是时候翻墙了。翻出去过后的答案也是牛头不对马嘴。

	忧郁了好一会，突然发现尼玛，这个包只有20+MB啊！！！！

**这次包下好了完全没问题**

安装中也出了一些问题。

但是我记不太清楚了0。0 
	
	其实是因为出的问题太多了，本来想安装成功过后做一个总结，之前查得解决方案的网页也都存了书签。

	可是，后来软件没装好，系统被玩坏了。。书签和历史记录全部丢失了，，我就有点懵逼了。。。

解决了一些问题过后，安装完成了。

**开心的atom想打开atom**

恩，提莫的。

error！ error！！

**version GLIBCXX_3.4.9 not found**

因为我解决问题的博文已经搞丢了，刚刚重新找了一篇大概差不多
[传送门](http://blog.csdn.net/officercat/article/details/39520227)

```php
ll /lib64/libc.so.6  
lrwxrwxrwx 1 root root 19 Sep 23 08:29 /lib64/libc.so.6 -> /lib64/libc-2.12.so  
```
libc.so.6是一个软连接，当前的glibc是2.12版本,我遇到的事GLIBC_2.15找不到的问题，所以需至少升级到2.15
首先，从网上下载glibc 2.15的rpm安装包，但这个不容易，因为.rpm针对的是centOS和redhat，高版本安装包很少见。也可以直接从其他系统上好一个编译好的文件
libc.so.6（对应glibc 2.15或者更高的），不过最保险的方式就是下载源代码在本地编译一次（有的人实在编译不成功，那也只能从别的地方找一份了）
各个版本的glibc可以从[下载地址](http://ftp.gnu.org/gnu/glibc/)找，包括其插件glibc-port
最新到2.20，我保守的选择2.15 
对于低版本glibc，还有glibc-linuxthreads-2.x需要编译，可参考很多网上文档，但2.15没有，所以不用了
```php 
wget http://ftp.gnu.org/gnu/glibc/glibc-2.15.tar.gz   
wget http://ftp.gnu.org/gnu/glibc/glibc-ports-2.15.tar.gz   
tar -xvf  glibc-2.15.tar.gz  
tar -xvf  glibc-ports-2.15.tar.gz   
mv glibc-ports-2.15 glibc-2.15/ports   
mkdir glibc-build-2.15    
cd glibc-build-2.15   
../glibc-2.15/configure  --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin  
make   
make install
```
如果提示install成功，去看glibc所在的共享库：

```
ll /lib64/libc* 
 ```
可以看到2.12的旧库文件还在，多了2.15版本的库文件，而且软链接文件全部指向了2.15版本。
<pre class="prettyprint linenums">
-rwxr-xr-x  1 root root 1921096 Aug 30 02:16 /lib64/libc-2.12.so  
-rwxr-xr-x  1 root root 9801632 Sep 25 13:46 /lib64/libc-2.15.so  
lrwxrwxrwx. 1 root root      18 May 19 18:51 /lib64/libcap-ng.so.0 -> libcap-ng.so.0.0.0  
-rwxr-xr-x. 1 root root   18672 Jun 25  2011 /lib64/libcap-ng.so.0.0.0  
lrwxrwxrwx. 1 root root      14 May 19 18:51 /lib64/libcap.so.2 -> libcap.so.2.16  
-rwxr-xr-x  1 root root   19016 Dec  8  2011 /lib64/libcap.so.2.16  
lrwxrwxrwx. 1 root root      19 May 19 18:57 /lib64/libcgroup.so.1 -> libcgroup.so.1.0.40  
-rwxr-xr-x  1 root root   97016 Dec  9  2013 /lib64/libcgroup.so.1.0.40  
-rwxr-xr-x  1 root root  197064 Aug 30 02:16 /lib64/libcidn-2.12.so  
-rwxr-xr-x  1 root root  267972 Sep 25 13:46 /lib64/libcidn-2.15.so  
lrwxrwxrwx  1 root root      15 Sep 25 13:52 /lib64/libcidn.so.1 -> libcidn-2.15.so  
lrwxrwxrwx. 1 root root      17 May 19 18:51 /lib64/libcom_err.so.2 -> libcom_err.so.2.1  
-rwxr-xr-x  1 root root   17256 Nov 22  2013 /lib64/libcom_err.so.2.1  
-rwxr-xr-x  1 root root   40400 Aug 30 02:16 /lib64/libcrypt-2.12.so  
-rwxr-xr-x  1 root root  142947 Sep 25 13:46 /lib64/libcrypt-2.15.so  
lrwxrwxrwx. 1 root root      22 May 19 18:57 /lib64/libcryptsetup.so.1 -> libcryptsetup.so.1.1.0  
-rwxr-xr-x  1 root root   97072 Jun 22  2012 /lib64/libcryptsetup.so.1.1.0  
lrwxrwxrwx  1 root root      16 Sep 25 13:52 /lib64/libcrypt.so.1 -> libcrypt-2.15.so  
lrwxrwxrwx  1 root root      12 Sep 25 13:52 /lib64/libc.so.6 -> libc-2.15.so  
</pre>

有些人会在make install后出现error。这儿error我没去细究,经过网友提醒，可能是因为没有sudo造成的，因为make install就是把文件拷贝到几个受保护的系统目录下。

如果还是不行，可以查看一下系统此时的GLIBC版本，参考一开始的做法。如果版本未升级，我们只能手动安装一下：

首先make是成功了，那么我们会发现build目录下编译出了一个新的libc.so.6 （/glibc-build-2.15/libc.so.6， 我们会发现这实际上也是一个软连接，真实的lib文件时libc.so， 输出

```php

$ ll  libc.so.6  
lrwxrwxrwx 1 root root 7 Sep 23 07:41 libc.so.6 -> libc.so  
$ strings libc.so | grep GLIBC  
GLIBC_2.2.5  
GLIBC_2.2.6  
GLIBC_2.3  
GLIBC_2.3.2  
GLIBC_2.3.3  
GLIBC_2.3.4  
GLIBC_2.4  
GLIBC_2.5  
GLIBC_2.6  
GLIBC_2.7  
GLIBC_2.8  
GLIBC_2.9  
GLIBC_2.10  
GLIBC_2.11  
GLIBC_2.12  
GLIBC_2.13  
GLIBC_2.14  
GLIBC_2.15  
GLIBC_PRIVATE  
```

这是我们需要的lib了，然后去更新系统的库。

这里要注意，更新系统里的链接（我的是/lib64/libc.so.6) 很容易出错，我不清楚有没有更好的办法，一般都是删除旧链接，建立新链接

但删除旧链接后，很多命令直接不能用了，因为此时中不到glibc的库了。这个时候就需要临时指定一个glibc库，方法如下（libc.so改个名以便好以后更新的其他版本区分）

```php
 cp /****/glibc-build-2.15/libc.so /lib64/libc-2.15.so  

rm -rf /lib64/libc.so.6  
LD_PRELOAD=/lib64/libc-2.15.so ln -s/lib64/libc-2.15.so  lib64/libc.so.6  
```

连接更新成功，再编译的话，GLIBC_2.15及以下版本的依赖问题就不会出现了。

>恩，到这里我也有话要说，我当时也是耿直，在这一步我重复了两三次！！！我耿直到从低版本一个一个往高版本安装啊。我真傻，真的。。。


***

** /usr/lib/libstdc++.so.6: version `GLIBCXX_3.4.14' not found**

[传送门](http://blog.csdn.net/xiaolong2w/article/details/23915171)

出现这种问题就是因为编译库的编译器和编译当前程序的编译器版本是不一样的，在具体一点就是因为，当前程序的编译器的版本是比较低的，只要升级一下就可以了。可以用如下命令查看一下当前GCC版本：

<pre class="prettyprint linenums">
strings /usr/lib/libstdc++.so.6 | grep GLIBCXX  
GLIBCXX_3.4  
GLIBCXX_3.4.1  
GLIBCXX_3.4.2  
GLIBCXX_3.4.3  
GLIBCXX_3.4.4  
GLIBCXX_3.4.5  
GLIBCXX_3.4.6  
GLIBCXX_3.4.7  
GLIBCXX_3.4.8  
GLIBCXX_3.4.9  
GLIBCXX_3.4.10  
GLIBCXX_3.4.11  
GLIBCXX_3.4.12  
GLIBCXX_3.4.13  
GLIBCXX_FORCE_NEW  
GLIBCXX_DEBUG_MESSAGE_LENGTH  
</pre>

并没有动态库中要求的GCC版本 “GLIBCXX_3.4.14”，所以需要进行升级一下我们的GCC版本，升级过程如下：

下载新版本的GCC，地址为[点击打开链接](http://ftp.de.debian.org/debian/pool/main/g/gcc-4.7/libstdc++6_4.7.2-5_i386.deb) （这里也有一个坑，今天真的是傻！！！）

下载下来之后，用如下命令开始解压
<pre class="prettyprint linenums">
ar -x libstdc++6_4.7.2-5_i386.deb && tar xvf data.tar.gz  
如果你的文件目录为GCC（如果不是，把GCC改为相应的目录即可），依次执行下面命令：


cd GCC/usr/lib/i380-linux-gun 
su root  
cp libstdc++.so.6.0.17 /usr/lib 
cd /usr/lib   
rm libstdc++.so.6  
ln -s libstdc++.so.6.0.17 libstdc++.so.6 
strings /usr/lib/libstdc++.so.6 | grep GLIBCXX  

GLIBCXX_3.4  
GLIBCXX_3.4.1  
GLIBCXX_3.4.2  
GLIBCXX_3.4.3  
GLIBCXX_3.4.4  
GLIBCXX_3.4.5  
GLIBCXX_3.4.6  
GLIBCXX_3.4.7  
GLIBCXX_3.4.8  
GLIBCXX_3.4.9  
GLIBCXX_3.4.10  
GLIBCXX_3.4.11  
GLIBCXX_3.4.12  
GLIBCXX_3.4.13  
GLIBCXX_3.4.14  
GLIBCXX_3.4.15  
GLIBCXX_3.4.16  
GLIBCXX_3.4.17  
GLIBCXX_DEBUG_MESSAGE_LENGTH  
</pre>
到这里其实就改结束了。可是回去atom 

恩。不错，不是原来的错误了，重新报了一个错误。（具体的错误我忘了）


后来在解决的时候才发现这个错误是因为我之前下载安装的libstdc是32位的，但是我的Atom是64位的就出错了。然后再这个过程中，我之前以为是我创建软链接的时候创建错误，我把lib和lib64里面的```libstdc++.so.6``` 都删了重新创建了一次。然后就GG了，这个时候连firefox浏览器都跟着崩溃了。后来意识到这个问题过后重新安装了64位的```libstdc++.so.6```解决了这个问题。

**undefined symbol g_bytes_unref**

这是遇到的最后的问题，也是彻底解决我的问题。。

在国内的网站上很少有这个问题的解决方法，后来找到一个论坛说是因为
```GBytes is relatively new, only since glib-2.32``` 准备更新glib库

[博文](http://blog.chinaunix.net/uid-26000296-id-5159364.html)

检查系统当前的版本
```php
 rpm -qi glibc
```

在CentOS中用更新命令到2.5已经是最新版本了，只好手动下载 glibc的源码包编译安装更新。
可以在 http://ftp.gnu.org/pub/gnu/glibc/看到当前的最新版本，
我看到的最新版本是glibc-2.22。

```php
 cd /usr/src 
 mkdir glibc-2.22-build 
 wget http://ftp.gnu.org/pub/gnu/glibc/glibc-2.22.tar.gz
 tar zxvf glibc-2.22.tar.gz 
 cd /usr/src/glibc-2.22-build/ 
 export CFLAGS="-g -O2 -march=i686" 
  网上有些地方加优化开关 -O2  时（大写字母O），写的是 -02 （数字零），
  所以拷贝过来仍然出现3中的错误，所以请注意。
 /usr/src/glibc-2.22-build/glibc-2.22/configure --prefix=/usr/src/glibc-2.22-build/ --with-headers=/usr/include
 make 
 make install
```
报错

```php
*** These critical programs are missing or too old: as ld compiler
*** Check the INSTALL file for required versions.

解决办法:
问题原因： 
缺少autoconf或版本太低


下载安装新版本


 wget ftp://ftp.gnu.org/gnu/autoconf/autoconf-2.68.tar.gz
 tar zxvf autoconf-2.68.tar.gz
 cd autoconf-2.68
 ./configure --prefix=/usr/
 make && make install

```

我进行到了这一步，然后虚拟机卡住了，卡了好久。。

然后我干出了今天干过最蠢的事**我直接用VM重启了它**（我的Windows卡住就是这样干的(尴尬脸)）

然后再开机我就懵逼了，我firefox的书签没了，历史记录没有，系统还报错。运行几分钟就一直卡住。。


然后我就决定果断弃坑！！！！！

PS： 

**这篇博文还很不完整，以后有机会再在Centos上装Atom成功的话在回来补充**
	
<pre class="prettyprint linenums">
多么痛的领悟
绝对不要随便强制重启！！！！
用浏览器一定要登录，书签和历史记录才能同步啊！！！
下次更新东西别再一个版本一个版本的往上装了，耿直过头啊！！！
</pre>