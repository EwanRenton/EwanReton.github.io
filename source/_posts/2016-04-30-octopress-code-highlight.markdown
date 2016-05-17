---
layout: post
title: "Octopress代码高亮问题（Windows10）"
date: 2016-04-30 00:57:01 +0800
comments: true
categories: 
---
>首先我要用祭出一段代码。。
<!--more-->
```php
<?php
	for($i=0;i<100;i++){
	echo "Octopress 的代码高亮真的真的有坑！！！"
}

?>
```

之前在写博客的时候就有发现，我的用markdown语法写出来的代码好像是和别人的不一样。后来发现是我的代码没有用高亮。人家也是一个有追求的PHPer（尴尬脸）。我就查markdown语法啊。
语法是这样给我说的

**markdown 语法实现代码高亮有两种方法**

	1  ```[language] [title] [url] [link text]
		 code snippet
	   ```
	2 {\% codeblock [title] [lang:language] [url] [link text] \%}
		code snippet
	{\% endcodeblock \%}
唉，原来这么简单。改代码走起->rake generate ->reke deploy

去看看我可爱的带颜色的代码呢。

！！！！！
为什么我的博客一片空白了！！！

然后我去度娘，去google。。

终于找到跟我一样懵逼的小伙伴[戳过去同情他](http://www.tuicool.com/articles/myqIba)
****
>按照这个小伙伴的思路我开始装Python

我装的是[Python2.7.11](https://www.python.org/downloads/release/python-2711/) 选择 Windows x86-64 MSI installer下载。然后一直下一步安装。

>安装完成后添加环境变量
[这个博客可以参考](http://www.blogjava.net/lishunli/archive/2012/03/18/372115.html)
鼠标右击我的电脑，选择属性，再选择高级系统设置，右下角有一个环境变量，点进去

![1](https://pic1.zhimg.com/6afe9ded178646e6c71e0c3fd908bdb8_b.jpg)

找到path项，选择编辑，把你的Python安装路径(如：C:\Python27\)输入进去，注意要用一个 ; 符号与前面的路径分开，再点确定就可以了。完事后可以运行cmd，输入python，如下显示就是成功了
![2](https://pic4.zhimg.com/7c56fca865253473857066067a0c053b_b.jpg)

>这个时候突然发现了另一篇博文

这个博文看起来要简单点[传送门](http://www.cnblogs.com/oec2003/archive/2013/05/27/3100896.html)

>按照下一篇博文的方法
<pre class="prettyprint linenums">
安装Python
安装Python,也是一路next就可以，博客的代码高亮用到了Python的Pygments模块，在Python中安装第三方库需要使用easy_install，在下面地址下载跟Python相对应的安装程序安装后就可以使用easy_install了。

https://pypi.python.org/pypi/setuptools
easy_install会安装在Python安装目录的Scripts目录中，例如我的Python目录是C:\Python27，所以需要将C:\Python27\Scripts目录加入到环境变量中才能在命令提示符中使用easy_install命令。

在命令提示符中输入如下命令就可以安装Pygments了。

easy_install pygments
</pre>
[https://pypi.python.org/pypi/setuptools](https://pypi.python.org/pypi/setuptools)去找到Windows (simplified)又懵逼了。它给了一段代码需要自己编译运行[ez_setup.py](https://bootstrap.pypa.io/ez_setup.py)新建文本文档将网页上的代码全部粘贴下来，保存重命名为ez_setup.py
<pre class="prettyprint linenums">
 Win+R
 cmd
 进到你刚刚保存的那文件的路径
 python ez_setup.py //安装easy_install
 等到安装完成过后
 将C:\Python27\Scripts目录加入到环境变量中前面的C:\Python27\和你之前添加的Python的环境变量一样
 easy_install pygments
 等到这一步完成后，基本就算大工告成了
</pre>

 如果你还是出现相同问题的话，那请找网管重启机器。

***
```php
<?php
	echo"不出意外的话你的问题也应该解决了。\n"
	echo"你看我是不是一段有颜色的代码啊！";
?>
```
#后记
>在解决上述问题的过程中，了解到另一种代码高亮的方法

[请戳这里](http://jingyan.baidu.com/article/67508eb4e660ed9cca1ce4ec.html)

使用google-code-prettify可以用来很方便地对网页中的程序代码进行高亮显示,从而在视觉上使得网页上的代码看得比较舒服,这对网站站长来说是很重要的。

有两种方法来启用google-code-prettify,下面简要介绍一下步骤。

>方法一


(1) 从https://code.google.com/p/google-code-prettify/downloads/list中下载或者从http://www.colafile.com/file/145953 中打包下载pretty.css以及pretty.js,并保存到本地服务器的相应目录中来引用

(2) 引用pretty.css以及pretty.js,然后在网页中引用,引用方法为

	<link href="你保存的目录/prettify.css" type="text/css" rel="stylesheet" />

	<script type="text/javascript" src="你保存的目录/prettify.js"></script>

(3) 

	<body>标签改为<body onload="prettyPrint()">

(4) 将源代码用标签

	<pre class="prettyprint linenums">这是你的代码</pre>

包含起来,例如

	<pre class="prettyprint linenums">

	#include "x.h"

	main(){

   	 print("hello world!");

	}

	</pre>

相应的显示效果为:
![3](http://hiphotos.baidu.com/exp/pic/item/0fb505d5ad6eddc4ef4d78d73bdbb6fd52663331.jpg)
-----
>方法二


(1) 到网址 [https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js](https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js) 中或者从[http://www.colafile.com/file/145957](http://www.colafile.com/file/145957) 下载run_prettify.js文件

(2) 引用run_prettify.js文件,方法为

	<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js"></script>

或者

	<script src="你保存的目录/run_prettify.js"></script>

(3) 将源代码用标签

	<pre class="prettyprint">这是你的代码</pre>

包含起来,例如

	<pre class="prettyprint">

	#include "x.h"

	main(){

    	print("hello world!");

	}

	</pre>

相应的显示效果为:

![4](http://hiphotos.baidu.com/exp/pic/item/72ccb7773912b31b40ea01058418367adbb4e1de.jpg)

如果你有更高的使用要求，可以到[https://code.google.com/p/google-code-prettify/wiki/GettingStarted](https://code.google.com/p/google-code-prettify/wiki/GettingStarted)这个网页中看更高级的使用方法
***
5/17/2016 8:28:40 PM  更新
>设置显示行号
>
<pre class="prettyprint linenums">
1、删掉prettify里的这两个样式。  
li.L0,li.L1,li.L2,li.L3,li.L5,li.L6,li.L7,li.L8{list-style-type:none}li.L1,li.L3,li.L5,li.L7,li.L9{background:#eee}  
  
2、替换以下内容，已经存在的替换，不存在的新增。  
pre.prettyprint {  
 padding: 8px;  
 background-color: #f7f7f9;  
 border: 1px solid #e1e1e8;  
}  
.prettyprint.linenums {  
 -webkit-box-shadow: inset 40px 0 0 #fbfbfc, inset 41px 0 0 #ececf0;  
 -moz-box-shadow: inset 40px 0 0 #fbfbfc, inset 41px 0 0 #ececf0;  
 box-shadow: inset 40px 0 0 #fbfbfc, inset 41px 0 0 #ececf0;  
}  
  
/* Specify class=linenums on a pre to get line numbering */  
ol.linenums {  
 margin: 0 0 0 33px; /* IE indents via margin-left */  
}   
ol.linenums li {  
 padding-left: 12px;  
 color: #bebec5;  
 line-height: 18px;  
 text-shadow: 0 1px 0 #fff;  
}  
3、在<pre>标签里面一定不能有<br/>标签,要换行直接回车.  
</pre>
***
#但是

这种方法在使用过程中，当是PHP代码时：如

	<pre class="prettyprint php">
		<?php
			echo "test!";
		?>
	</pre>
这样在实际的显示过程中只有块黑色的代码块，代码并没解析出来。

改成如下格式就能正常实现代码高亮效果。

		<pre class="prettyprint php">
			//去掉了PHP的开始和结束符
			echo "test!";
		
		</pre>

我没有去具体研究这个原理，不过我推测是因为PHP的开始副 <?  和pre的开始结束符产生了混乱不能正确匹配，从而导致解析失败。