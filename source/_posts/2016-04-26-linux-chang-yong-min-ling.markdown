---
layout: post
title: "Linux-chang-yong-min-ling"
date: 2016-04-26 18:55:40 +0800
comments: true
categories: [Linux]
---
>Linux 命令总结


#一、常用目录作用
* / 根目录
* /bin 命令保存目录（普通用户可以读取的命令）
* /boot	启动目录，启动相关文件
* /dev	设备文件保存目录
* /etc	配置文件目录
* /home	普通用户家目录
* /lib	系统库保存目录
* /mnt	系统挂载目录
* /media	挂载目录
* /root	超级用户的家目录
* /tmp	临时目录
* /sbin	命令保存目录（超级用户才能使用）
* /proc	直接写入内存
* /sys	~
* /usr	系统软件资源目录
	* /usr/bin	系统命令（普通用户）
	* /usr/sbin	系统命令（超级用户）

* /var	系统相关文档

---
#二、常用基本命令
##2.1 目录和文件处理命令
* mkdir [选项] [目录名] 创建目录
	* -p 递归创建

* rm [选项] [目录名] 删除
	* -r 删除目录
	* -f 强制

* mv [原文件或目录] [目标目录] 剪切或改名
	* 没有选项 剪切文件和目录使用相同命令
* cp [选项] [原文件或目录] [目标目录]
	* -r 复制目录
	* -p 连带文件属性复制
	* -d 若源文件是连接文件，则赋值连接属性
	* -a 相当于-pdr 
* cd 
	* ~ 进入家目录
	* - 进入上次目录
	* .. 进入上级目录
	* . 进入当前目录

* pwd 显示当前所在位置


* ls [选项] [目录]
	* -l 长格式 详细信息
	* -h 人性化显示大小 如-lh 会把大小变成能看懂的格式如128k
	* -a 显示所有文件
	* -d 查看目录属性
	* -i 显示inode id号

##2.2链接命令
* 硬链接 不推荐 ln  [原文件] [目标文件]
	* 拥有相同的i节点和存储block块，可以看做是同一个文件。相当于一个文件的两个接入。
	* 可以通过i节点识别
	* 不能跨分区
	* 不能针对目录使用

* 软链接
	* 类似Windows快捷方式
	* 软链接拥有自己的I节点和Block块，但是数据中只保存原文件的文件名和节点号，并没有实际文件数据
	* lrwxrwxrwx l 软链接 软链接的权限都为 rwxrwxrwx、
	* 修改任意文件，另一个都改变
	* 删除原文件，软链接不能使用
* ln -s [原文件] [目标文件] 软链接 （原文件一定要写绝对路径）

##2.3 文件搜索命令
* 配置文件 /etc/updatedb.conf
	* PRUNE_BIND_MOUNTS="yes" #开启器搜索限制
	* PRUNEFS = #搜索时不搜索的文件系统
	* PRUNENAMES = #搜索时不搜索的文件系统
	* PRUNEPATHS = # 搜索时不搜多的路径
* locate [文件名] 在后台数据库按文件名搜索，搜索速度较快
	* /var/lib/mlocate #locate命令搜索的数据库
	* updatadb 强制更新数据库（默认一天一更新）

* whereis [选项] [命令] 搜索命令的命令 输出命令位置和帮助文档位置  只能查系统命令
	* -b 只看命令所在位置
	* -m 只看帮助文档所在位置

* which [命令] 找到命令和别命
* find [搜索范围] [搜索文件] 
	* find / -name install.log
	* 避免大范围搜索，非常消耗系统资源
	* *匹配任意内容
	* ？匹配任意一个字符
	* []任意中括号之内的一个内容
	* find / -iname install.log 不区分大小写
	* find / -user root 按所有者搜索
	* find / -user -nouser 搜索没有所有者的文件
	* find /var/log -mtime +10 查找十天前修改的文件
		* atime 文件访问时间 -10 十天内
		* ctime 改变文件属性  10 十天当天
		* mtime 修改文件内容 +10 十天前

	* find . -size 25k 查找当前目录25k的文件
		* -25 小于25k
		* +25 大于25k

	* find . -inum xxx 查找当前目录i节点为XX的文件
	* find /etc -size +20k -a -50k  ect下大于20k小于50k的问价
		* -a  and 逻辑与
		* -o  or  逻辑或
	* find /etc -size +20k -a -50k -exec ls -lh {} \;将查找到的文件LS出来
		* -exec/-ok 命令 {} \ ; 对搜索结果执行操作、

* grep [选项] 字符串 文件名 在文件中匹配符合条件的字符串
	* -i 忽略大小写
	* -v 排除指定字符串

##2.4帮助命令
* man  命令
	* man一共有1-9级 man -f 命令 查看命令有几个帮助级别
	* man [1-9] 命令 查看现有级别的帮助

* --help	或许选项帮助
	* ls --help 获取选项
* help shell内部命令 获取shell内部命令帮助
	* 用 whereis cd 来看是否为内部命令，没有执行文件则为内部命令
	* 如 help cd
* info 详细命令帮助 太复杂 不常用
#2.5压缩与解压缩命令
* 常用压缩格式：.zip .gz .bz2 .tar.gz .tar.bz2
* zip [目标文件] [源文件]
	* zip -r [压缩目录名] [源目录]
	* unzip [压缩文件] 解压缩
* gzip [源文件] 生成.gz文件 #源文件消失
	* gzip -c 源文件 > 压缩文件 #压缩为.gz 源文件不消失
	* gzip -r 压缩目录下所有子文件 但是不能压缩目录
	* gzip -d 压缩包 #解压缩
	* gunzip 压缩包 #解压缩
* bz2 不能压缩目录
	* bzip2 源文件  #不保留源文件
	* bzip2 -k 源文件  #保留源文件
	* bzip2 -d 压缩包  #-k保留源文件
	* bunzip2  压缩包  #-k保留源文件
* tar -jcvf 压缩包名.tar.bz2 源文件
	* -z 压缩为.tar.gz
	* -c 打包
	* -v 显示打包过程
	* -f 指定打包后的文件名

* tar -jxvf 压缩包名.tar.bz2 加 -C 指定解压缩位置
	* -z 解压缩.tar.gz
#2.6关机重启命令
* shutdown [选项] [时间] & #加&表示在后台运行
	* -c 取消前一个关机命令
	* -h 关机
	* -r 重启

* poweroff
* reboot

* init 【0-6】
	* 0 关机
	* 1 单用户
	* 2 不完全多用户，不含NFS
	* 3 完全多用户
	* 4 未分配
	* 5 图形界面
	* 6 重启

#2.7挂载命令

* mount #查看系统已挂载的设备
* mount -a #根据配置文件/etc/fstab 的内容 下的设备自动挂载
* mount [-t 文件系统] [-o 特殊选项] 设备文件名 挂载点
	* -t ext3 ext4 iso9660（光盘）

* 挂载光盘
	1. mkdir /mnt/cdroom
	2. mount -t iso9660 /dev/sr0 /mnt/cdrom/ 其中-t在这里可省略因为系统知道sr0为光盘，知道它的文件系统。
	3. umount 设备文件名或挂载点 #卸载 必须卸载！！！

* 挂载U盘
	1. fdisk -l 查看系统中已经识别的硬盘
	2. mount -t vfat /dec/sdbx/mnt/usb/ #sdbX通过上一条命令查看可知 
	3. Linux默认不支持NTFS文件系统，需要下载ntfs-3g安装可以挂载ntfs但是也只能读。


#2.8其他常用命令
* 用户登录查看
	* w  #查看登录登录用户的信息 输出一些信息 包括占用cpu等
	* who  
	* 输出 用户名 登录登录终端 登录时间和IP
	* last 查询登录日志 /var/log/wtmp
	* lastlog 查看每个用户的最后登录信息/var/log/lastlog