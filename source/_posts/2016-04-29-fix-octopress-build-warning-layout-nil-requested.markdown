---
layout: post
title: "解决 Octopress 警告 Layout Nil Requested"
date: 2016-04-29 23:08:07 +0800
comments: true
categories: [Octopress]
---
>给Octopress换了主题过后重新generate会出警告Layout Nil Requested。

<!--more-->
主要原因是对于 nill 值无法判断导致的问题，只要将有出现错误的档案將 nill 改成 null 即可


Fix this error by replacing from file:
>layout: nil


with:
>layout: null


in files:

* source/atom.xml
* source/_includes/custom/category_feed.xml
