---
layout: post
title: "PHP性能优化初识"
date: 2016-05-04 22:46:21 +0800
comments: true
categories: [PHP]
---
>PHP性能优化
<!--more-->
#一、PHP性能优化问题解析

PHP的性能问题，占整体项目性能问题的比例不足50%，所以在考虑性能优化项目的时候，不要局限于仅优化PHP。

PHP的性能优化问题的解决方向（困难度从容易到简单）

	PHP语言级的性能优化
	PHP周边问题的性能优化（开发环境等）
	PHP语言自身分析、优化（底层C）

##压力测试工具简介
**Apache Benchmark（ab）**

简介

	ab是由Apache提供的压力测试软件。安装Apache服务器时会自带该压测软件。

如何使用
	
	./ab -n1000 -c100 http://www.baidu.com （目标网址）
	-n 请求数
	-c 并发数
	url 目标压测地址

结果分析
	
	主要看
	Request per second 每秒接受的请求数
	Time per request   一个请求的耗时

#二、PHP语言级的优化
**优化点：少写代码，多用PHP自身能力,多使用PHP内置的函数，变量，常量**

**性能问题：自写代码冗余较多，可读性不佳，并且性能低**

为什么性能低？

	我们自己写的代码每次需要编译解析成底层语言，每一次请求都会处理一遍开销很大。

***
**PHP内置函数的性能优劣**

情况描述：

	PHP内置函数之间依然存在着快慢差异

多去了解PHP内置函数的时间复杂度

举例
```php
<?php
	$start=current_time();
	$i=0;
	$arr=range(1,200000);
	while($i<200000){
		++$i;
		//isset($arr[$i]); //测试结果50多ms
		array_key_exists($i,$arr); //测试结果80多ms
	}
	$end=current_time();
	echo "Lost Time:".number_format($end-$start,3)*1000;
	echo "\n";
	/**
	 *返回一个微秒级别的时间戳
	 */
	function current_time(){
		list($usec,$sec)=explode("",microtime());
		return ((float)$usce+(float)$sec);
	}
?> 
```
测试结果说明isset比array_key_exists函数更优化。
***
**减少PHP魔法函数的使用**

情况描述：

	PHP提供的魔法函数性能不佳

举例
```php
<?php
class test{
	private $var="123";
	public function __get($varname){
		return $this->var;
	}
}
$i=0;
while($i<10000-){
	$i++;
	$test=new test();
	$test->var;
}//大概80ms
?>
```

```php
<?php
class test{
	public $var="123";
	//public function __get($varname){
		//return $this->var;
	//}
}
$i=0;
while($i<100000){
	$i++;
	$test=new test();
	$test->var;
}//大概50ms
?>
```

不使用魔法函数，性能更优化。

PS：
	
	在Linux中可以使用 time php test.php
	会输出三个时间，主要关注user为执行时间

***
**产生额外开销的错误抑制符@**

情况描述：

	@的实际逻辑是在代码开始前结束后增加Opcode 忽略报错。
	在@这行前加入Opcode忽略报错，又在这行结束后再加入Opcode，恢复报错。

```php
<?php
	file_get_contents("XXX");
	
?>
```
用VLD扩展看Opcode代码：

	php -dvld.active=1 -dvld.execute=0 at.php //我们只看代码不执行

	有三行代码
	加上@符号后
	有五行代码

建议不要使用错误抑制符@，
多使用try catch
***
**合理使用内存**

情况描述：

	PHP有内存回收机制保底，但也要小心使用内存。

利用unset（）及时释放不适用的内存（注：unset（）出现注销不掉的情况）
***
**尽量少的使用正则表达式**

情况描述：

	正则表达式的回溯开销较大。
***
**避免在循环内做运算**

情况描述:

	循环内的计算式将会被重复计算

```php
<?php
	$str="Hello World!"
	for($i=0;$i<strlen($str);$i++){//每次循环都会计算这个字符串的长度
		//do something
	}
?>
```
改进：

```php
<?php
	$str="Hello World!"
	$strlen=strlen($str)
	for($i=0;$i<$strlen;$i++){
		//do something
	}
?>
```
***
**减少计算密集型业务**

情况描述：

	PHP的语言特性决定了PHP不适合密集型运算的场景。
	因为PHP是基于C语言的，所以在处理大型运算的时候开销是比C大很多的。

适合的场景：

	衔接Webserver与后端服务，UI呈现。
***
**务必使用带引号字符串做键值**

情况描述：

	PHP会将没有引号的键值当做常量，产生查找常量的开销。

***
#三、PHP周边问题的性能优化

**Linux 运行环境**

**减少文件类的操作**

常见PHP场景的开销次序：

	读写内存<<读写数据库（数据库有内存缓存）<读写磁盘<读写网络数据（网络延迟）

***
**优化网络请求**

可能的问题：
	1. 对方接口的不确定因素
	2. 网络稳定性

优化：

	1. 设置超时时间
		连接超时（２００ｍｓ）
		读超时（８００ｍｓ）
		写超时（５００ｍｓ）
	２.　串行请求并行化
		使用curl_multi_*() //取决于最长的连接时间所以不推荐
		使用swoole扩展　　

***
**压缩PHP接口输出**

使用Gzip压缩即可

优点：

	利于我们的数据输出，Client能更快获取数据
缺点：

	产生cpu额外开销
	
可以考虑当数据大于多少时再使用压缩来优化。
***
**缓存重复计算的内容**

什么情况下：
	
	多次请求，内容不变 //使用模板渲染缓存 如smarty 开启caching
	
***
**重叠时间窗口思想和旁路方案**

	在几个任务不强依赖前一个任务的输出或者返回时。可以在上一个任务没完成时就执行下一个任务
***
#四、PHP性能分析
**用XHPorf进行PHP性能问题的具体分析**

XHPorf：源自Facebook的PHP性能优化工具
其他工具：
	
	ab -压力测试
	vld -opcode 代码分析

[here](http://www.imooc.com/video/4275)
#五、PHP性能瓶颈究极办法

Opcode Cache ： PHP扩展APC，memcache

通过PHP扩展代替原PHP代码中高频逻辑

Runtime优化： HHVM



	

