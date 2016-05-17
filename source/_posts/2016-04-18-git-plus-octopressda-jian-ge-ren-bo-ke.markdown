---
layout: post
title: "GIT+Octopress搭建个人博客"
date: 2016-04-18 23:13:10 +0800
comments: true
categories: [Git,Octopress]
---
#GIT+Octopress搭建个人博客
<!--more-->
>新博客的第一篇博客



很久以前就想自己搭个博客，今天终于开工了。在网上找了很多的教程和各种各样的框架，wordpress，jekyll等，最后选择了Octopress+Git搭建。
##一、环境搭建#
###[GIT](https://git-scm.com/download/):版本管理工具，将代码托管到GIT。
###[Ruby+DevKit](http://rubyinstaller.org/) ：生成静态页面
###[MarkDownPad](http://jingyan.baidu.com/article/ca41422fe209271eaf99ed7c.html)：Windows下MarkDown语法编辑器
####安装与配置#

	GIT和Markdown安装直接点击下一步就可以了。
	在安装好git过后，桌面或其他位置点击右键GIT Bash 来配置Github账号（需要先注册GITHUB账号）
***
1.1配置GITHUB

	```
	git config --global user_name "XXXX"//你的github账号
	git config --global user_email "XXXX"//你的邮箱
	ssh-keygen -t rsa -C "YOUR_EMAIL"
	```
下面回车默认为空即可

然后在将你系统盘（大多为C盘）->用户->当前用户（就是你装系统的时候给电脑取的名字）->.ssh文件夹下有id_rsa和id_rsa.pub两个文件打开后者全选复制。
添加到你的GITHUB的SSH KEYs （GITHUB登录后的setting里面）

回到你的bash 
```
ssh -T git@github.com
```

来测试SSH keYS,提示HI XXX! 则成功
 ***
1.2配置RUBY

	一直下一步在选择安装路径的时候记得勾选第二个选项添加到你的系统路径。	
	安装完成后继续安装DevKit，一直下一步即可，记得选择安装目录。


进到DevKit安装目录，打开Bash

```
ruby dk.rb init
ruby dk.rb install
```
 ***
1.3安装Octopress

	去到你要保存Octopress的位置打开Bash
	git clone git：//github.com/imathis/octopress.git octopress//获取Octopress
	cd octopress
	gem sources -a https://ruby.taobao.org//添加国内RUBY软件源。在WINDOWS系统下有可能会失败 提示SSL错误。
建议使用
$ bundle config mirror.https://rubygems.org https://gems.ruby-china.org 
[参考网址](https://gems.ruby-china.org/)
 ***


```
gem install bundler
bundler install
rake install
```

以上步奏完成并无ERROR则环境搭建成功

rake genreate //生成静态页面

rake preview //这行代码会在本地打开4000端口作为测试

你可以打开浏览器 输入 localhost：4000 来访问你的站点。
####生成博客与单页面#
***
1.新建博客

	rake new_post["title"] //生成名为title的博客
***
2.新建单页面

	 rake new_page[test]//会生成source/test/index.markdown
	 rake new_page[test/page.html]//会生成source/test/page.html
	
需要使用markdown语法来书写博客
***
3.生成并本地预览

	rake generate
	rake preview //http://localhost:4000
####部署网站到GITHUB#
***
1.新建仓库

	username.github.io
***
2.与本地Octopress 目录绑定

	rake setup_github_pages//这里会需要你输入你的GITHUB地址，请输入：git@github.com:yourname/yourname.github.com.git (将yourname替换成你的github登录名)

注意:这一步不要输入提示中提示的以io结束的地址。
	rake deploy

成功过后就可以通过访问
username.github.io 来访问你的个人博客了
***
PS： 第一次用自己搭的博客写博客，markdown语法用起来也不太熟悉。

	
	
	