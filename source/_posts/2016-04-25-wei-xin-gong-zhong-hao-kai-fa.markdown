---
layout: post
title: "微信公众号开发"
date: 2016-04-25 14:19:37 +0800
comments: true
categories: [PHP,Weixin]
---
#微信公众号开发
>上星期突然突发奇想，想弄个微信公众号来玩。然后再网上找了一些教程过就开始了。


***



##一、前期准备
###1.1一个[微信公众号](https://mp.weixin.qq.com/)
###1.2[SAE](http://sae.sina.com.cn) 用来放你的代码
***
##二、开始开发
###2.1公众号的分类

	公众号分为服务号，订阅号，和企业号三种
	简单的来说：
	订阅号：你关注了过后会放进订阅号那个分类里的一些列号，每天能推送消息但是不能主动给你发消息（除非你在某个时间段内给它发了消息）
	服务号：关注了过后和你的微信好友是在同级的，它可以给你发消息。
	企业号：微信的新儿子，主要客户是面向企业。
	我们这里主要说订阅号，订阅号又会分为几种，每种订阅号的API接口调用权限也是不同的。
###2.2SAE
	在申请好SAE后，进入到控制台，创建新应用输入二级域名等一些列信息过后，运行环境选择你对应的开发语言，我选择的是PHP5.3然后选择空应用，创建。
	回到控制台，你会看到你刚刚新建的应用，点进去过后选择代码管理，你可以看到你的GIT仓库地址，之后你把你的代码push到这个仓库就可以了。
###2.3下载官方接口程序文件
	在微信公众平台左侧找到开发->开发者工具->进入开发者文档->开始开发->接入指南->第二步最后有一个PHP实例代码下载。将它下载下来。
###2.4公众号的配置
	在申请完公众号后，登陆过后在管理页面的左边功能里有群发功能，自动回复等，这些操作是不需要涉及到代码的，我们不再多讲，我们主要讲的是开发者模式下的开发。
	首先，在左侧菜单最后找到开发->基本配置->启用服务器配置->分别填写URL，Token，EncodingAESKey。其中URL就是你在SAE控制台应用名字下面那个URL http://xxx.applinzi.com Token 在你下载的代码中去找（默认为weixin，你也可以自己修改，只要他们保持一致即可。）EncodingAESKey选择随机生成即可。消息加解密方式，默认就行。提交过后我们的公众号就配置好了。
###2.5 Hello World !程序测试


```
<?php
/**
  * wechat php test
  */

//define your token
define("TOKEN", "weixin");
$wechatObj = new wechatCallbackapiTest();
if($_GET['echostr'])
{
    $wechatObj->valid();
}else
{
    $wechatObj->responseMsg();
}



class wechatCallbackapiTest
{
	public function valid()
    {
        $echoStr = $_GET["echostr"];

        //valid signature , option
        if($this->checkSignature()){
        	echo $echoStr;
        	exit;
        }
    }

    public function responseMsg()
    {
		//get post data, May be due to the different environments
		$postStr = $GLOBALS["HTTP_RAW_POST_DATA"];

      	//extract post data
		if (!empty($postStr)){
                /* libxml_disable_entity_loader is to prevent XML eXternal Entity Injection,
                   the best way is to check the validity of xml by yourself */
                libxml_disable_entity_loader(true);
              	$postObj = simplexml_load_string($postStr, 'SimpleXMLElement', LIBXML_NOCDATA);
                $fromUsername = $postObj->FromUserName;
                $toUsername = $postObj->ToUserName;
                $keyword = trim($postObj->Content);
                $time = time();
                $textTpl = "<xml>
							<ToUserName><![CDATA[%s]]></ToUserName>
							<FromUserName><![CDATA[%s]]></FromUserName>
							<CreateTime>%s</CreateTime>
							<MsgType><![CDATA[%s]]></MsgType>
							<Content><![CDATA[%s]]></Content>
							<FuncFlag>0</FuncFlag>
							</xml>";             
	if(!empty( $keyword ))//判断用户发送的信息不为空则回复Hello Wordld！
                {
              		$msgType = "text";
                	$contentStr = "Hello World!";
                	$resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                	echo $resultStr;
                }else{
                	echo "Input something...";
                }

        }else {
        	echo "";
        	exit;
        }
    }
		
	private function checkSignature()
	{
        // you must define TOKEN by yourself
        if (!defined("TOKEN")) {
            throw new Exception('TOKEN is not defined!');
        }
        
        $signature = $_GET["signature"];
        $timestamp = $_GET["timestamp"];
        $nonce = $_GET["nonce"];
        		
		$token = TOKEN;
		$tmpArr = array($token, $timestamp, $nonce);
        // use SORT_STRING rule
		sort($tmpArr, SORT_STRING);
		$tmpStr = implode( $tmpArr );
		$tmpStr = sha1( $tmpStr );
		
		if( $tmpStr == $signature ){
			return true;
		}else{
			return false;
		}
	}
}

?>
```

	
	将代码push到SAE上过后，给公众号发任意消息就能收到HelloWorld！回复。

	实现了HelloWorld！实例，还有很多的回复图文，根据关键字实现多客服接入，以及一些其他的API功能调用，这些都可以参考官方的开发者文档。

##三、进阶知识
###3.1公众平台测试账号
	由于我们自己的订阅号很多API不能使用，就需要通过测试账号（可以调用所有的API）来调用其他API
###3.2access_token 
	公众号的全局唯一票据，公众号调用各接口时都需要使用access_token
###3.3网页授权接口
	通过网页授权接口来获取用户的openID(用户关注某一个公众号的唯一标示，且关注不同的公众号openid是不同的)和基本信息。
###3.4JSSDK
	可在网页中调用手机拍照，语言，选图等系统功能。
###3.5模板消息
	模板消息可以主动给用户发送信息并且不受时间限制
	
除了以上功能外，微信公众平台还有很多的API接口可以调用，用以实现不同的功能，但是基本常用的API在开发文档上都有详细的教程，或者到baidu API 去检索你需要的功能。