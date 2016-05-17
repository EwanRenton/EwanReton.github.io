---
layout: post
title: "PHP无限级分类"
date: 2016-04-29 09:36:19 +0800
comments: true
categories: [PHP]
---
>PHP无限级分类主要有两种实现方式，通过递归和全路径实现
<!--more-->
#一、递归实现
	在数据库中存储如下格式的表：

|ID|pid|catename|cateorder|createtime|
|--|:--:|:--:|:--:|--:|
|1|0|新闻|0|0|
|2|0|图片|0|0|
|3|1|国内新闻|0|0|
|4|1|国际新闻|0|0|
|5|3|北京新闻|0|0|
|6|4|美国新闻|0|0|
|7|2|美女图片|0|0|
|8|2|风景图片|0|0|

```
create table deepcate
(
id int(10) unsigned	not null  auto_increment primary key,
pid	int(11)	unsigned	not null,
catename	varchar(30)	not null,
cateorder	int(11)	unsigned	default 0,
createtime	int(10)	not null
);

insert into deepcate(pid,catename,cateorder,createtime)values(0,'新闻',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(0,'图片',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(1,'国内新闻',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(1,'国际新闻',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(3,'北京新闻',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(4,'美国新闻',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(2,'美女图片',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(2,'风景图片',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(7,'日韩明星',0,0);
insert into deepcate(pid,catename,cateorder,createtime)values(9,'日本AV',0,0);

select * from deepcate;
```
>php 代码实现

```
<?php
header("Content-Type:text/html;Charset=UTF-8");
// php无限分类下拉列表的代码实现
include_once('db.inc.php') ;

function getList($pid=0,&$result=array(),$spac=0)
{
    $spac=$spac+2;
    $sql=" SELECT * FROM deepcate WHERE pid=$pid ";
    $res=mysql_query($sql);
   
    while($row=mysql_fetch_assoc($res)){
        $row['catename']=str_repeat('&nbsp;&nbsp;',$spac).'|--'.$row['catename'];
        $result[]=$row;
        getList($row['id'],$result,$spac);
    }
    return $result;
}

function displayCate($pid=0,$selected=0){
     $rs=getList($pid);
     $str="";
     $str.="<select name='cate'>";
    foreach($rs as $v){
        if($v['id']==$selected){
            $selected="selected";
        }
       $str.="<option {$selected}>";
       $str.= $v['catename'];
       $str.= "</option>";
    }
    return $str.= "</select>";
}

echo displayCate();

echo "<hr/><hr/>";
function getCatePath($cid,&$result){
    $sql="SELECT * FROM deepcate WHERE id=$cid ";
    $rs=mysql_query($sql);
    $row=mysql_fetch_assoc($rs);
    if($row){
        $result[]=$row;
        getCatePath($row['pid'],$result);
    }
    krsort($result);
    return $result;
}


function displayCatePath($cid,$url="deepcate.php?cid="){
    $res=getCatePath($cid,$result);
    $str="";
    foreach($res as $v){
        $str.= "<a href='{$url}{$v['id']}'>{$v['catename']}</a>>>";
    }return $str;
}

echo displayCatePath(10);
```
	

#二、全路径实现
```
use imooc;
create table	likecate
(
id	int(11)	not null  auto_increment primary key,
path	varchar(200)	not null,
catename	varchar(30)	not null,
cateorder	int(11)	not null	default 0,
createtime	int(11)	not null
);

insert into likecate(path,catename,cateorder,createtime)values('','手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1','功能手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1,2','老人手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1,2','儿童手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1','智能手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1,5','andriod手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1,5','ios手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1,5','winphoto手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1,2,4','色盲手机',0,0);
insert into likecate(path,catename,cateorder,createtime)values('1,2,3','大字手机',0,0);

select * from likecate;

select id,catename,path,concat(path,',',id) as fullpath from likecate where 1 order by fullpath asc;

#结果显示如图
+----+--------------+-------+----------+
| id | catename     | path  | fullpath |
+----+--------------+-------+----------+
|  1 | 手机         |       | ,1       |
|  2 | 功能手机     | 1     | 1,2      |
|  3 | 老人手机     | 1,2   | 1,2,3    |
| 10 | 大字手机     | 1,2,3 | 1,2,3,10 |
|  4 | 儿童手机     | 1,2   | 1,2,4    |
|  9 | 色盲手机     | 1,2,4 | 1,2,4,9  |
|  5 | 智能手机     | 1     | 1,5      |
|  6 | andriod手机  | 1,5   | 1,5,6    |
|  7 | ios手机      | 1,5   | 1,5,7    |
|  8 | winphoto手机 | 1,5   | 1,5,8    |
+----+--------------+-------+----------+
```
>php代码实现

```
<?php
header("Content-Type:text/html;Charset=UTF-8");
//递归无限分类原理
include_once ('db.inc.php');
 
function likecate(){
    $sql="select id,catename,path,concat(path,',',id) as fullpath from likecate where 1 order by fullpath asc";
    $res=mysql_query($sql);
    $result=array();
    while($row=mysql_fetch_assoc($res)){
        $deep=count(explode(',',trim($row['fullpath'],',')));
         
        $row['catename']=str_repeat('&nbsp;&nbsp;',$deep*4).'|--'.$row['catename'];
        $result[]=$row;
    }
    return $result;
}
 
 
$res=likecate();
echo "<select name='cate'>";
foreach($res as $v){
    echo "<option>{$v['catename']}</option>";
}
echo "</select>";
 
echo "<hr/><hr/>";
function getPathCate($cateid){
    $sql="select *,concat(path,',',id)fullpath from likecate where id=$cateid";
    $res=mysql_query($sql);
    $row=mysql_fetch_assoc($res);
    $ids=$row['fullpath'];
    $sql="select * from likecate where id in($ids) order by id asc";
    $res=mysql_query($sql);
    $result=array();
    while($row=mysql_fetch_assoc($res)){
        $result[]=$row;
    }
    return $result;
}
 
function displayCatePath($cateid,$link='likecate.php?cid='){
    $res=getPathCate($cateid);
    $str="";
    foreach($res as $v){
        $str.="<a href='{$link}'>{$v['catename']}</a>>";
    }
    return $str;
}  
 
echo displayCatePath(4,'likecate.php?p=1&cid=');
```