---
title: 'hexo yalia主题添加文章阅读量-小白适用'
toc: true
copyright: true
date: 2018-04-04 19:32:59
tags: ['hexo']
---

本人是web前端小白。以下给出配置hexo yalia主题的文章阅读量的步骤
<!--more-->

### 1. ejs相关了解

因为参考的文章配置之后，阅读量显示有bug,所以，稍微看了下ejs相关的东西，之后，修复了bug。(为自己点赞!)

参考[ejs的简要介绍](https://www.jianshu.com/p/81ea81d291fd),可以知道一下几点内容：

- 1.ejs是一个JavaScript模板库，用来从Json数据库中生存html字符串
- 2.在html中引入ejs，以使javascript能够使用它
    简单来说，就是可以和html混着用，可以调用js代码生成html,或者是调用js代码调用函数，将结果生成到html中
- 3.简单的ejs语法
    <% code %>          执行其中的javascript代码
    <%= code %>         会对code的内容进行转义
    <%- code %>          不会对code内容进行转义

稍微知道点ejs语法，就可以读懂代码了。

### 2. 存储库申请和配置(leancloud)

[leancloud](https://leancloud.cn/)是一个云数据库，用来存储文章阅读量的查询。

根据[这篇博客](http://www.icafebolger.com/hexo/hexopostcount.html)申请配置leancloud。
注意,创建应用时候，应用名称可以自己随意起，但是**创建存储Class的时候，名字也要叫Counter**，后面配置hexo的时候会用到,**如果名字不叫Counter记得去修改**。
看完创建API类就可以了，后面的内容在下面会给出配置成功的方法。

### 3. 配置Hexo

 1.在**yilia**主题下修改**_config.yml**，最后添加是否使用leancloud进行阅读量统计的相关配置：
  填上你在leancound创建的应用的 appid和appkey
```
# 添加浏览量
leancloud_visitors:
  enable: true
  app_id: **************        # 填上你在leancound创建的应用的 appid和appkey
  app_key: ************

#添加一下js插件的 CDN地址
js_cdn:
  jquery: https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js
  av : //cdn1.lncld.net/static/js/2.5.0/av-min.js
```

 2.在主题下的**layout\_parial\post**文件夹里面，新建**leancloud.ejs**文件，内容如下：

```
<script src="<%- theme.js_cdn.av %>"></script>
<script type="text/javascript"> 
if(<%- theme.leancloud_visitors.enable %>){
    var leancloud_app_id  = '<%- theme.leancloud_visitors.app_id %>';
    var leancloud_app_key = '<%- theme.leancloud_visitors.app_key %>';

    AV.init({
        appId: leancloud_app_id,
        appKey: leancloud_app_key
    });

    var pageViewsLength = $(".pageViews").length;

    var isIndex = $(".pageViews").length > 1 ?true:false;

    function showTime() {
        var Counter = AV.Object.extend("Counter");          // leanclound查询ClassName叫做 Counter 的查询
        if(isIndex){
            $(".pageViews").each(function(){
                showPageViewsNum($(this),Counter);         // 注意这里，只需要显示在列表中的阅读数量，不需要阅读数+1
            });
        }else{
            showPageViewsNum($(".pageViews"),Counter);
            addPageViewsNum($(".pageViews"));
        }
    }

    //显示阅读量
    function showPageViewsNum(ele,Counter){
        var query = new AV.Query("Counter");
        var url = ele.attr('id').trim();
            query.equalTo("words", url);
            query.count().then(function (number) {
                $(document.getElementById(url)).text(number?  number : '0');
            }, function (error) {
                 $(document.getElementById(url)).text('0');
            });
    }

    //追加阅读量
    function addPageViewsNum(ele){
        var url = ele.attr('id').trim();
        var Counter = AV.Object.extend("Counter");          // 查询leanclound叫做Counter的查询
        var query = new Counter;
        query.save({
            words: url
        }).then(function (object) {});
    }

    if(pageViewsLength){ //此处判断等于 1 在执行 可去除循环
        showTime();
    }
}
</script>
```

根据我们刚才稍微了解到的ejs知识，可以阅读代码。这里就是调用显示阅读数量和阅读数量+1的地方
**注意1：代码中isIndex为ture的时候，是在主页中显示的列表。这里只需要显示阅读数量，不需要阅读数量+1**
**注意2：AV.Object.extend("Counter"); 的意思是，在leanclound查询ClassName叫做 Counter 的查询,如果之前*<font color='gray'>创建存储Class</font>*的时候，不叫Counter，这里要换成你的命名**

 3.在主题下的**layout\_parial\post** 找到 title.ejs 文件,添加显示阅读量的html代码:
```
<% if (post.link){ %>
  <h1 itemprop="name">
    <a class="<%= class_name %>" href="<%- url_for(post.link) %>" target="_blank" itemprop="url"><%= post.title %></a>
  </h1>
<% } else if (post.title){ %>
  <% if (index){ %>
    <h1 itemprop="name">
      <a class="<%= class_name %>" href="<%- url_for(post.path) %>"><%= post.title %></a>
    </h1>
  <% } else { %>
    <h1 class="<%= class_name %>" itemprop="name">
      <%= post.title %>
    </h1>
  <% } %>
<% } %>
<!-- 上面是 yilia 主题的标题 下面是添加阅读量代码 -->
<!-- span 给的 id 是文章的 url 作为唯一的 key 值 -->
<% if (theme.leancloud_visitors.enable){ %>
  <a href="javascript:;" class="archive-article-date">
      <i class="icon-smile icon"></i> 阅读数:<span id="<%- url_for(post.path) %>" class="pageViews">0</span>次
  </a>
<% } %>
<!-- 自己添加结束 -->

```

 4.添加js代码, 在**layout\_parial**找到**after.footer.ejs**,在**</script>**后面再添加一段script代码:
```
<script>
    var yiliaConfig = {
        mathjax: <%=theme.mathjax%>,
        isHome: <%=is_home()%>,
        isPost: <%=is_post()%>,
        isArchive: <%=is_archive()%>,
        isTag: <%=is_tag()%>,
        isCategory: <%=is_category()%>,
        open_in_new: <%=theme.open_in_new%>,
        toc_hide_index: <%=theme.toc_hide_index%>,
        root: "<%=config.root%>",
        innerArchive: <%=theme.smart_menu.innerArchive ? true : false%>,
        showTags: <%=(theme.slider && theme.slider.showTags) ? true : false%>
    }
</script>

<!--上面是 yilia 主题配置自带 下面是自己添加的 -->
<script src="<%- theme.js_cdn.jquery %>"></script>

<% if (theme.leancloud_visitors.enable){ %>
<%- partial('post/leancloud') %> 
<% } %>

<!--自己添加结束 -->

<%- partial('script') %>

<% if (theme.mathjax){ %>
<%- partial('mathjax') %>
<% } %>

```
这里添加的内容是说，如果leancloud_visitors.enable是ture,就调用post文件加下的leancloud.ejs。
怎么样，稍微懂了一点ejs知识，一下子就明了了吧？


以上。
