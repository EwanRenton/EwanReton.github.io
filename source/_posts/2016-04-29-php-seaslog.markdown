---
layout: post
title: "PHP高性能日志SeasLog"
date: 2016-04-29 09:59:55 +0800
comments: true
categories: [PHP]
---
>PHP高性能日志SeasLog

#一、什么是SeasLog

SeasLog是一个C语言编写的PHP扩展，提供一组规范标准的功能函数，在PHP项目中方便、规范、高效地写日志，以及快速地读取和查询日志。

#二、为什么需要日志系统
1. 了解系统运行情况
2. 记录用户操作信息
3. 收集需要

#三、为什么选择SeasLog
1. 高性能 带有缓冲池功能。
2. 无需配置
3. 功能完善，使用简单
#四、SeasLog安装
	到[pecl](http://pecl.php.net/)搜索seaslog并下载
	解压后
	可自行编译。
```
$ cd seaslog //进到解压目录
$ phpize
$ ./configure --with-php-config=/path/to/php-config //path to 为你的PHP安装目录
$ make && make install
当然，使用PECL管理工具会更方便：
$ pecl install seaslog
```

##seaslog.ini的配置
```
; configuration for php SeasLog module 
extension = seaslog.so 
seaslog.default_basepath = /log/seaslog-test    ;默认log根目录 
seaslog.default_logger = default                ;默认logger目录 
seaslog.disting_type = 1                            ;是否以type分文件 1是 0否(默认) 
seaslog.disting_by_hour = 1                      ;是否每小时划分一个文件 1是 0否(默认) 
seaslog.use_buffer = 1                              ;是否启用buffer 1是 0否(默认) 
seaslog.buffer_size = 100                         ;buffer中缓冲数量 默认0(不使用buffer_size) 
seaslog.level = 0                                       ;记录日志级别 默认0(所有日志)
```
**需要将这里面的数据写到php.ini中去**
 
	seaslog.disting_type = 1 开启以type分文件，即log文件区分info\warn\erro
	seaslog.disting_by_hour = 1 开启每小时划分一个文件
	seaslog.use_buffer = 1 开启buffer。默认关闭。当开启此项时，日志预存于内存，当请求结束时(或异常退出时)一次写入文件。
	seaslog.buffer_size = 100 设置缓冲数量为100. 默认为0,即无缓冲数量限制.当buffer_size大于0时,缓冲量达到该值则写一次文件.
	seaslog.level = 3 记录的日志级别.默认为0,即所有日志均记录。当level为1时,关注debug以上级别(包括debug)，以此类推。level大于8时，所有日志均不记录。

##默认常量有哪些
遵循PSR-3标准，SeasLog 共将日志分成8个级别

	SEASLOG_DEBUG "debug"
	SEASLOG_INFO "info"
	SEASLOG_NOTICE "notice"
	SEASLOG_WARNING "warning"
	SEASLOG_ERROR "error"
	SEASLOG_CRITICAL "critical"
	SEASLOG_ALERT "alert"
	SEASLOG_EMERGENCY "emergency"

##都提供哪些方法

```

<?php 
/** 
 * @author neeke@php.net 云智慧 
 */ 
 
class SeasLog 
{ 
    public function __construct() 
    { 
        #SeasLog init 
    } 
 
    public function __destruct() 
    { 
        #SeasLog distroy 
    } 
 
    /** 
     * 设置basePath 
     * @param $basePath 
     * @return bool 
     */ 
    static public function setBasePath($basePath) 
    { 
        return TRUE; 
    } 
 
    /** 
     * 获取basePath 
     * @return string 
     */ 
    static public function getBasePath() 
    { 
        return 'the base_path'; 
    } 
 
    /** 
     * 设置模块目录 
     * @param $module 
     * @return bool 
     */ 
    static public function setLogger($module) 
    { 
        return TRUE; 
    } 
 
    /** 
     * 获取最后一次设置的模块目录 
     * @return string 
     */ 
    static public function getLastLogger() 
    { 
        return 'the lastLogger'; 
    } 
 
    /** 
     * 统计所有类型（或单个类型）行数 
     * @param string $level 
     * @param string $log_path 
     * @param null $key_word 
     * @return array | long 
     */ 
    static public function analyzerCount($level = 'all',$log_path = '*',$key_word = NULL) 
    { 
        return array(); 
    } 
 
    /** 
     * 以数组形式，快速取出某类型log的各行详情 
     * @param $level 
     * @param string $log_path 
     * @param null $key_word 
     * @param int $start 
     * @param int $limit 
     * @return array 
     */ 
    static public function analyzerDetail($level = SEASLOG_INFO,$log_path = '*',$key_word = NULL, $start = 1,$limit = 20) 
    { 
        return array(); 
    } 
 
    /** 
     * 获得当前日志buffer中的内容 
     * @return array 
     */ 
    static public function getBuffer() 
    { 
        return array(); 
    } 
 
    /** 
     * 记录debug日志 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function debug($message,array $content = array(),$module = '') 
    { 
        #$level = SEASLOG_DEBUG 
    } 
 
    /** 
     * 记录info日志 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function info($message,array $content = array(),$module = '') 
    { 
        #$level = SEASLOG_INFO 
    } 
 
    /** 
     * 记录notice日志 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function notice($message,array $content = array(),$module = '') 
    { 
        #$level = SEASLOG_NOTICE 
    } 
 
    /** 
     * 记录warning日志 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function warning($message,array $content = array(),$module = '') 
    { 
        #$level = SEASLOG_WARNING 
    } 
 
    /** 
     * 记录error日志 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function error($message,array $content = array(),$module = '') 
    { 
        #$level = SEASLOG_ERROR 
    } 
 
    /** 
     * 记录critical日志 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function critical($message,array $content = array(),$module = '') 
    { 
        #$level = SEASLOG_CRITICAL 
    } 
 
    /** 
     * 记录alert日志 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function alert($message,array $content = array(),$module = '') 
    { 
        #$level = SEASLOG_ALERT 
    } 
 
    /** 
     * 记录emergency日志 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function emergency($message,array $content = array(),$module = '') 
    { 
        #$level = SEASLOG_EMERGENCY 
    } 
 
    /** 
     * 通用日志方法 
     * @param $level 
     * @param $message 
     * @param array $content 
     * @param string $module 
     */ 
    static public function log($level,$message,array $content = array(),$module = '') 
    { 
 
    } 
} 
```
##在项目中如何使用
>获取与设置basePatn
>


```
/**
 *静态方法可以不实例化直接使用
 *
 */
$basePath_1 = SeasLog::getBasePath(); 
 
SeasLog::setBasePath('/log/base_test'); 
$basePath_2 = SeasLog::getBasePath(); 
 
var_dump($basePath_1,$basePath_2); 
 
/* 
string(19) "/log/seaslog-ciogao" 
string(14) "/log/base_test" 
*/ 
```
直接使用 SeasLog::getBasePath()，将获取php.ini(seaslog.ini)中设置的seaslog.default_basepath 的值。

使用 SeasLog::getBasePath() 函数，将改变 seaslog_get_basepath() 的取值。

>设置logger与获取lastLogger

```
$lastLogger_1 = SeasLog::getLastLogger(); 
 
SeasLog::setLogger('testModule/app1'); 
$lastLogger_2 = SeasLog::getLastLogger(); 
 
var_dump($lastLogger_1,$lastLogger_2); 
/* 
string(7) "default" 
string(15) "testModule/app1" 
*/ 
```

与basePath相类似的，
直接使用 SeasLog::getLastLogger()，将获取php.ini(seaslog.ini)中设置的seaslog.default_logger 的值。

使用 SeasLog::setLogger() 函数，将改变 SeasLog::getLastLogger() 的取值。

##SeasLog Logger的使用

>获取与设置basePath


```
$basePath_1 = SeasLog::getBasePath();

SeasLog::setBasePath('/log/base_test');
$basePath_2 = SeasLog::getBasePath();

var_dump($basePath_1,$basePath_2);

/*
string(19) "/log/seaslog-ciogao"
string(14) "/log/base_test"
*/
```

直接使用 SeasLog::getBasePath()，将获取php.ini(seaslog.ini)中设置的 seaslog.default_basepath 的值。

使用 SeasLog::setBasePath() 函数，将改变 SeasLog::getBasePath() 的取值。

>设置logger与获取lastLogger


```
$lastLogger_1 = SeasLog::getLastLogger();

SeasLog::setLogger('testModule/app1');
$lastLogger_2 = SeasLog::getLastLogger();

var_dump($lastLogger_1,$lastLogger_2);
/*
string(7) "default"
string(15) "testModule/app1"
*/
```

与basePath相类似的，

直接使用 SeasLog::getLastLogger()，将获取php.ini(seaslog.ini)中设置的 seaslog.default_logger 的值。

使用 SeasLog::setLogger() 函数，将改变 SeasLog::getLastLogger() 的取值。
快速写入log

上面已经设置过了basePath与logger，于是log记录的目录已经产生了，

log记录目录 = basePath / logger / {fileName}.log log文件名，以 年月日 分文件，如今天是2014年02月18日期，那么 {fileName} = 20140218;
还记得 php.ini 中设置的 seaslog.disting_type 吗？

默认的 seaslog.disting_type = 0，如果今天我使用了 SeasLog ，那么将产生最终的log文件：

*　LogFile = basePath / logger / 20140218.log
如果 seaslog.disting_type = 1，则最终的log文件将是这样的三个文件

* infoLogFile = basePath / logger / INFO.20140218.log

* warnLogFile = basePath / logger / WARN.20140218.log

* erroLogFile = basePath / logger / ERRO.20140218.log

```
SeasLog::log(SEASLOG_ERROR,'this is a error test by ::log');

SeasLog::debug('this is a {userName} debug',array('{userName}' => 'neeke'));

SeasLog::info('this is a info log');

SeasLog::notice('this is a notice log');

SeasLog::warning('your {website} was down,please {action} it ASAP!',array('{website}' => 'github.com','{action}' => 'rboot'));

SeasLog::error('a error log');

SeasLog::critical('some thing was critical');

SeasLog::alert('yes this is a {messageName}',array('{messageName}' => 'alertMSG'));

SeasLog::emergency('Just now, the house next door was completely burnt out! {note}',array('{note}' => 'it`s a joke'));


/*
这些函数同时也接受第3个参数为logger的设置项
注意，当last_logger == 'default'时等同于:
SeasLog::setLogger('test/new/path');
SeasLog::error('test error 3');
如果已经在前文使用过SeasLog::setLogger()函数，第3个参数的log只在此处临时使用，不影响下文。
*/
```
>log格式统一为： {type} | {pid} | {timeStamp} |{dateTime} | {logInfo}



	error | 23625 | 1406422432.786 | 2014:07:27 08:53:52 | this is a error test by log
	debug | 23625 | 1406422432.786 | 2014:07:27 08:53:52 | this is a neeke debug
	info | 23625 | 1406422432.787 | 2014:07:27 08:53:52 | this is a info log
	notice | 23625 | 1406422432.787 | 2014:07:27 08:53:52 | this is a notice log
	warning | 23625 | 1406422432.787 | 2014:07:27 08:53:52 | your github.com was down,please rboot it ASAP!
	error | 23625 | 1406422432.787 | 2014:07:27 08:53:52 | a error log
	critical | 23625 | 1406422432.787 | 2014:07:27 08:53:52 | some thing was critical
	emergency | 23625 | 1406422432.787 | 2014:07:27 08:53:52 | Just now, the house next door was completely burnt out! it is a joke
##SeasLog Analyzer的使用

>快速统计某类型log的count值



SeasLog在扩展中使用管道调用shell命令 grep -wc快速地取得count值，并返回值(array || int)给PHP。
```
$countResult_1 = SeasLog::analyzerCount();
$countResult_2 = SeasLog::analyzerCount(SEASLOG_WARNING);
$countResult_3 = SeasLog::analyzerCount(SEASLOG_ERROR,date('Ymd',time()));

var_dump($countResult_1,$countResult_2,$countResult_3);
/*
array(8) {
  ["debug"]=>
  int(3)
  ["info"]=>
  int(3)
  ["notice"]=>
  int(3)
  ["warning"]=>
  int(3)
  ["error"]=>
  int(6)
  ["critical"]=>
  int(3)
  ["alert"]=>
  int(3)
  ["emergency"]=>
  int(3)
}

int(7)

int(1)

*/
```
>获取某类型log列表



SeasLog在扩展中使用管道调用shell命令 grep -w快速地取得列表，并返回array给PHP。
```
$detailErrorArray_inAll   = SeasLog::analyzerDetail(SEASLOG_ERROR);
$detailErrorArray_today   = SeasLog::analyzerDetail(SEASLOG_ERROR,date('Ymd',time()));

var_dump($detailErrorArray_inAll,$detailErrorArray_today);

/*
SeasLog::analyzerDetail(SEASLOG_ERROR) == SeasLog::analyzerDetail(SEASLOG_ERROR,'*');
取当前模块下所有level为 SEASLOG_ERROR 的信息列表:
array(6) {
 [0] =>
  string(66) "error | 8568 | 1393172042.717 | 2014:02:24 00:14:02 | test error 3 "
  [1] =>
  string(66) "error | 8594 | 1393172044.104 | 2014:02:24 00:14:04 | test error 3 "
  [2] =>
  string(66) "error | 8620 | 1393172044.862 | 2014:02:24 00:14:04 | test error 3 "
  [3] =>
  string(66) "error | 8646 | 1393172045.989 | 2014:02:24 00:14:05 | test error 3 "
  [4] =>
  string(66) "error | 8672 | 1393172047.882 | 2014:02:24 00:14:07 | test error 3 "
  [5] =>
  string(66) "error | 8698 | 1393172048.736 | 2014:02:24 00:14:08 | test error 3 "
}

SeasLog::analyzerDetail(SEASLOG_ERROR,date('Ymd',time()));
只取得当前模块下，当前一天内,level为SEASLOG_ERROR 的信息列表:
array(2) {
  [0] =>
  string(66) "error | 8568 | 1393172042.717 | 2014:02:24 00:14:02 | test error 3 "
  [1] =>
  string(66) "error | 8594 | 1393172044.104 | 2014:02:24 00:14:04 | test error 3 "
}

同理，取当月
$detailErrorArray_mouth = SeasLog::analyzerDetail(SEASLOG_ERROR,date('Ym',time()));

*/
```
