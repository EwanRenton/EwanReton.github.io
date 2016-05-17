---
layout: post
title: "Octpress自定义配置和主题安装"
date: 2016-04-30 20:57:43 +0800
comments: true
categories: 
---
>Octpress 基本配置
<!--more-->
在_config.yml文件中。可以直接修改title，author等基本信息。	

<pre class="prettyprint linenums">
# ----------------------- #
#      Main Configs       #
# ----------------------- #

url: http://ewanreton.github.io
title: Ewan Reton 
subtitle: Never be touched by yourself
author: Ewan Reton
simple_search: https://www.baidu.com
description: 

# Default date format is "ordinal" (resulting in "July 22nd 2007")
# You can customize the format as defined in
# http://www.ruby-doc.org/core-1.9.2/Time.html#method-i-strftime
# Additionally, %o will give you the ordinal representation of the day
date_format: "ordinal"

# RSS / Email (optional) subscription links (change if using something like Feedburner)
subscribe_rss: /atom.xml
subscribe_email:
# RSS feeds can list your email address if you like
email: liukedi001@gmail.com
</pre>
下一部分的Jekyll & Plugins 在没有对Octpress进行深入了解时不介意修改。
在下一部分的3rd Party Settings   就是一些部件的配置，我们今天主要的内容就是涉及这一块的配置。
>使用第三方插件

 * 增加评论功能
 * 增加社会化分享
 * 自定义404页面
 * 自定义导航
 * 安装使用主题
 * 设置链接在新窗口打开
 * 首页文章以摘要形式展示
 * 添加侧边栏文章分类（category）
 * 自动为图片添加url前缀
 * 添加访客统计

>增加评论

	默认使用discuss，但是由于discuss在国内使用效果不好所以用国内多说来替代

1. 在多说网站注册过后，在工具->获取代码 将这块代码复制。
2. 在配置文件中添加
<pre class="prettyprint linenums">
	#duoshuo
	duoshuo_comment = true //开启多说
	在sourse 中 找到_layout post.html
</pre>
```php
	{% if site.disqus_short_name and page.comments == true %}
	<section id="comment">
    <h1 class="title">Comments</h1>
    <div id="disqus_thread" aria-live="polite">{% include post/disqus_thread.html %}</div>
	</section>
	{% endif %}
```

<pre class="prettyprint text">
	将其中的site.disqus_short_name and page.comments改为我们刚刚配置的duoshuo_comment
	并将其中引用的文件disqus_thread.html改为duoshuo_comment.html
	如下：
</pre>

```php
	{% if duoshuo_comment == true %}
	<section id="comment">
    <h1 class="title">Comments</h1>
    <div id="disqus_thread" aria-live="polite">{% include post/duoshuo_comment.html %}</div>
	</section>
	{% endif %}
	
```
并修改duoshuo_comment.html的内容

```html
<!-- 多说评论框 start -->
	<div class="ds-thread" data-thread-key="{{page.id}}" data-title="{{page.title}}" data-url="{{site.url}}{{page.url}}"></div>
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"ewanreton"};
	(function() {
		var ds = document.createElement('script');
		ds.type = 'text/javascript';ds.async = true;
		ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
		ds.charset = 'UTF-8';
		(document.getElementsByTagName('head')[0] 
		 || document.getElementsByTagName('body')[0]).appendChild(ds);
	})();
	</script>
<!-- 多说公共JS代码 end -->
```

>社会化分享

同上一样，先在_config.yml中添加
```php
#jiashare
jia_share = true;
```
打开source->_include->post->sharing.html
```php
<div class="sharing">

	{% if site.facebook_like %}
	<a class="addthis_button_facebook_like" fb:like:layout="button_count"></a>
	{% endif %}
	{% if site.twitter_tweet_button %}
	<a class="addthis_button_tweet"></a>
	{% endif %}
	{% if site.google_plus_one %}
	<a class="addthis_button_google_plusone" g:plusone:size="{{ site.google_plus_one_size }}"></a>
	{% endif %}
	
</div>
```
添加代码。如下：
```php
<div class="sharing">

	{% if site.facebook_like %}
	<a class="addthis_button_facebook_like" fb:like:layout="button_count"></a>
	{% endif %}
	{% if site.twitter_tweet_button %}
	<a class="addthis_button_tweet"></a>
	{% endif %}
	{% if site.google_plus_one %}
	<a class="addthis_button_google_plusone" g:plusone:size="{{ site.google_plus_one_size }}"></a>
	{% endif %}
	{% if site.jia_share %}
	{% include post/jia_share.html %}
	{% endif %}
</div>
```
并创建jia_share.html
```html 这段代码在jiathis网站获取
	<!-- JiaThis Button BEGIN -->
<div class="jiathis_style_32x32">
	<a class="jiathis_button_qzone"></a>
	<a class="jiathis_button_tsina"></a>
	<a class="jiathis_button_tqq"></a>
	<a class="jiathis_button_weixin"></a>
	<a class="jiathis_button_renren"></a>
	<a href="http://www.jiathis.com/share" class="jiathis jiathis_txt jtico jtico_jiathis" target="_blank"></a>
	<a class="jiathis_counter_style"></a>
</div>
<script type="text/javascript" src="http://v3.jiathis.com/code/jia.js" charset="utf-8"></script>
<!-- JiaThis Button END -->
```

>自定义404页面

直接打开source下的404.markdown进行编辑即可
>自定义导航

新建一个页面 rake new_page['about me'];

在source->_inlcude->custom->navigation.html
```php
	<ul class="main">
    <li><a href="{{ root_url }}/">Blog</a></li>
    <li><a href="{{ root_url }}/blog/archives">Archives</a></li>
</ul>
```

添加代码如下：


```php
	<ul class="main">
    <li><a href="{{ root_url }}/">Blog</a></li>
    <li><a href="{{ root_url }}/blog/archives">Archives</a></li>
	<li><a href="{{ root_url }}/aboutme">About Me</a></li>
</ul>
```
>安装使用主题


```php
 到GitHub上找主题文件
 git clone git：//github.com/tommy351/Octopress-Theme-Slash.git .themes/slash //会在theme主题下生成对应名称的文件夹
 rake install ['主题名称']
```
**注意：**
	在上面安装主题前记得备份，因为安装主题过后你之前配置的信息都会被重置（如评论和分享）
***
>设置链接在新窗口打开

在博文中，如果点击链接直接在本窗口打开了，那么用户体验就不是很好。而markdown的标准语法是不支持链接在新窗口打开的，虽然可以通过在markdown中直接写html标签来解决这个问题，但是这与markdown的简洁书写特性不符。但是我们可以通过设置Octopress来达到这种效果，即在 \source\_includes\custom\head.html 文件中添加如下一段代码
```java
<script>
  function addBlankTargetForLinks () {
    $('a[href^="http"]').each(function(){
      $(this).attr('target', '_blank');
    });
  }
  $(document).bind('DOMNodeInserted', function(event) {
    addBlankTargetForLinks();
  });
</script>
```
>首页文章以摘要形式展示

1. 在文章对应的markdown文件中，在需要显示在首页的文字后面添加 <!--more--> ，执行```rake generate```后在首页上会看到只显示<!—more—>前面的文字，文字后面会显示 ```Read on``` 链接，点击后进入文字的详细页面;

2. 如果想将Read on修改为中文，可以修改_config.yml文件

```php
excerpt_link: "Read on →"  # "Continue reading" link text at the bottom of excerpted articles
修改为：
excerpt_link: "阅读全文→"  # "Continue reading" link text at the bottom of excerpted articles
```

>添加侧边栏文章分类（category）

1. 在 plugins 目录下创建 category_list_tag.rb 文件，内容如下：

```php
module Jekyll 
  class CategoryListTag < Liquid::Tag 
    def render(context) 
      html = "" 
      categories = context.registers[:site].categories.keys 
      categories.sort.each do |category| 
        posts_in_category = context.registers[:site].categories[category].size 
        category_dir = context.registers[:site].config['category_dir'] 
        category_url = File.join(category_dir, category.gsub(/_|\P{Word}/, '-').gsub(/-{2,}/, '-').downcase) 
        html << "<li class='category'><a href='/#{category_url}/'>#{category} (#{posts_in_category})</a></li>\n" 
      end 
      html 
    end 
  end 
end

Liquid::Template.register_tag('category_list', Jekyll::CategoryListTag)
```

2. 添加 source/_includes/asides/category_list.html 文件，内容如下：

```java
<section>
  <h1>文章分类</h1>
  <ul id="categories">
    \{\% category_list \%\} //去掉\
  </ul>
</section>
```
3. 修改 _config.yml 文件，在 default_asides 项中添加 asides/category_list.html ，值之间以逗号隔开，值的先后顺序代表了侧边栏展现的先后顺序。

```java
default_asides: [asides/category_list.html, asides/recent_posts.html, asides/github.html, asides/delicious.html, asides/pinboard.html, asides/googleplus.html]
```
>自动为图片添加url前缀

把图片资源放在了七牛云存储 上，写博客时候就是用七牛的外链。但是这样有几个问题：

* 每次写博客插入图片外链地址时候都很麻烦，需要给每张图片都添加七牛外链地址url前缀；
* 如果以后更换了存储，那就麻烦了，需要依次编辑替换每个图片的url前缀
现在我们就使用一种灵活的方式来配置并自动生成图片的URL前缀：

首先修改 /plugins/image_tag.rb 文件，在 @img['class'].gsub!(/"/, '') if @img['class'] 后添加下面一行代码：

```php ./plugins/image_tag.rb
@img['src'] = Jekyll.configuration({})['static_file_prefix'] + @img['src'] if @img['src'][0] == '/' 
```
然后再修改根目录下的 ```_config.yml``` 文件，添加如下配置：
```php
# Add url prefix for image automatically
static_file_prefix: http://7u2i08.com1.z0.glb.clouddn.com
```
最后我们在插入图片的时候要记住不能再使用Markdown语法来写了，要使用Ocotpress自定义的IMG标签来插入图片 。
```php
{% img http://placekitten.com/890/280 %}
{% img left http://placekitten.com/320/250 Place Kitten #2 %}
{% img right http://placekitten.com/300/500 150 250 Place Kitten #3 %}
{% img right http://placekitten.com/300/500 150 250 'Place Kitten #4' 'An image of a very cute kitten' %}
```

本地预览先generate后preview，这样一来插入图片就灵活方便多了。

>添加访客统计

本博客的访客统计系统使用的是Flag Counter，所以要 先去Flag Counter获取代码 。

拿到代码后添加``` .\source\_includes\custom\asides\flag_counter.html``` 文件：

```java  flag_counter.html
<section>
  <h1>访客统计</h1>
  <br/>
  <a href="http://s07.flagcounter.com/more/2SH"><img src="http://s07.flagcounter.com/count/2SH/bg_FFFFFF/txt_000000/border_CCCCCC/columns_2/maxflags_12/viewers_0/labels_0/pageviews_1/flags_0/" alt="Flag Counter" border="0"></a>
</section>
```

将页面添加到侧边栏，在 ./_config.yml 配置文件中添加下面一行配置：
```php
default_asides: [……, custom/asides/flag_counter.html]
```
最后添加控制开关，在 ./_config.yml 配置文件中添加下面一行配置：
```php
# Flag Counter
flag_counter: true
```
