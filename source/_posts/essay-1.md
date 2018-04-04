---
title: '0.hexo常用命令'
date: 2018-04-02 15:07:02
tags: '随笔'
description: '简单描述'
copyright: true
---

新搭建完成自己的博客,记录一下常用的命令什么的

<!--more-->

一、命令：
常见命令：
```
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
```
缩写命令：
```
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```
组合命令
```
hexo s -g #生成并本地预览
hexo d -g #生成并上传

```

二、文章格式： （如果题目什么的是中文，要添加单引号''或者双引号""）
```

---
title: postName #文章页面上的显示名称，一般是中文
date: 2013-12-02 15:30:16 #文章生成时间，一般不改，当然也可以任意修改
categories: 默认分类 #分类
tags: [tag1,tag2,tag3] #文章标签，可空，多标签请用格式，注意:后面有个空格
description: 附加一段文章摘要，字数最好在140字以内，会出现在meta的description里面
toc: true # 如果设置了自动生成目录
copyright: true # 如果设置了添加版权信息。添加版权方法链接 https://amazer.github.io/2018/04/02/hexo-addcopyright/
---

正文xxxxxx

```
[添加版权方法链接](https://amazer.github.io/2018/04/02/hexo-addcopyright/)

三、摘要和详细内容
合适的位置加
```
<!--more-->

```
