---
layout: post
title: "Nginx下ThinkPHP应用Url重写"
date: 2016-05-16 23:36:12 +0800
comments: true
categories: [nginx]
---
>在调Nginx的虚拟主机时发现，我之前的TP改写的商城没放过来（之前挂载BAE上），把BAE上的项目移植过来过后发现，只能访问主页其他页面全部都报404错误

到处查资料过后发现，是因为nginx下ThinkPHP应用的url重写和Apache有点不一样。

[官方手册](http://www.kancloud.cn/manual/thinkphp/1866)说在Nginx低版本中，是不支持PATHINFO的，但是可以通过在Nginx.conf中配置转发规则实现：
<pre class="prettyprint linenums">
 location / { // …..省略部分代码
   if (!-e $request_filename) {
   rewrite  ^(.*)$  /index.php?s=$1  last;
   break;
    }
 }
</pre>
但是好像对我并不适用，在网上找到一篇博文成功解决了问题。

[原文链接](http://blog.jjonline.cn/linux/159.html)

按照博文的方法在我的虚拟主机配置中（/etc/nginx/conf.d/shop.ewanreton.cn.conf）添加了如下代码：
<pre class="prettyprint linenums">
location / {
 root /usr/share/nginx/html/shop;
 index index.html index.htm index.php;
 if (!-e $request_filename) {
 rewrite ^/index.php(.*)$ /index.php?s=$1 last;
 rewrite ^(.*)$ /index.php?s=$1 last;
 break;
 }
}
</pre>
这里我仅仅对我用ThinkPHP框架写的应用进行了配置，如果需要所有都配置的话，推荐直接写在Nginx的配置文件中。

虽然解决了这个问题，但是对这个问题的核心还是没有搞太懂，以后深入学习过后再回来补充这篇文章。