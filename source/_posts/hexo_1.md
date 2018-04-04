---
title: hexo添加版权信息
date: 2018-04-02 17:51:50
tags: 'hexo'
copyright: true
toc: true
---

参考了几篇文章添加版权信息，但是由于本人不是html前端，很多东西都搞不懂，参考文章里面的东西总是配置不对。

但是最后按照自己的放置配置了一下。以下列出配置过程： 

<!--more-->

##### <a name="t1">1.新建版权信息文件</a>

在themes\yilia\layout\_partial\post下新建**copyright.ejs**,内容如下

```
<! -- 添加版权信息 -->
<! -- 设置版权信息格式 -->
<style type="text/css">
div.article-footer-copyright{
   border-top:1px solid #d3d3d3;
   margin: 20px auto;     
   padding-left:2em;
   width: 80%;
}
div.article-footer-copyright span,.copyright abbr{
  color:#3d3d3d;
}
div.article-footer-copyright{
	margin-top:2em;
	padding:1.5em;
	border:1px solid #d3d3d3;
	background-color:#DEEBF7;
}
div.article-footer-copyright {
	line-height: 160%;
	margin: 10px;
	font-size: 90%;
    color: #A3A3A3
}
</style>
<br>
<!-- 版权信息内容在这里 -->
<div class="article-footer-copyright">
<center>转载请注明作者及出处,本文作者为<b><a href="<%= config.root %>index.html" target="_blank" title="<%= config.author %>"><%= config.author %></a></b>,本文标题为<b><a href="<%- config.root %><%- post.path %>" target="_blank" title="<%= post.title %>"><%= post.title %></a></b></center>

<center>本文链接为<b><a href="<%- config.root %><%- post.path %>" target="_blank" title="<%= post.title %>"><%- config.url %>/<%- post.path %></a></b></center>
</div>
<! -- 添加版权信息 -->

```

##### 2.修改article.ejs
在themes\yilia\layout\_partial\\**article.ejs**中添加如下代码,调用版权信息:

```
<article id="<%= post.layout %>-<%= post.slug %>" class="article article-type-<%= post.layout %> <%if(index){%> article-index<%}%>" itemscope itemprop="blogPost">
  <div class="article-inner">
    <% if (post.link || post.title){ %>
      <header class="article-header">
        <%- partial('post/title', {class_name: 'article-title'}) %>
        <% if (!post.noDate){ %>
        <%- partial('post/date', {class_name: 'archive-article-date', date_format: null}) %>
        <% } %>
      </header>
    <% } %>
<!-- 在这里添加版权内容-->
    <% if(!index && post.copyright==true) { %>
            <%- partial('post/copyright') %>
    <% } %>
<!--添加版权内容结束-->

```
意思是说,有找到index，并且解析post返回的copyright为true的时候，调用我们刚才添加的copyright.ejs内容

这里可以自己改变添加版权内容的位置，可以添加在文章开头或者文章结尾。


##### 3.添加头部信息

在文章的头部信息中添加**copyright: true**,如下
```

---
title: 延澈左的文章
copyright: true
---

```

##### 4.修改博客的url

修改_config.yml中的url,改成自己的地址。注意，是themes文件夹同一层目录下的_config.yml文件。
如下：

```
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://amazer.github.io/              # 这里修改成你的地址
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

```

##### 5.其他

在<a href="#t1">第一节</a>中，直接定义了需要的style格式。可以修改成自己喜欢的样式。
参考文章中是通过定义styl文件获取样式，但是，我使用的hexo都用的是.scss文件。然而并不懂scss和styl之间怎么转换，能不能调用。因此使用的简单粗暴的方式解决。

emmm...虽然不是很完美，但是先有再追求。


以上。

