---
title: '[hexo] 多端同步win7下使用(yilia主题)'
copyright: true
date: 2018-04-05 12:40:16
toc: true
tags: hexo
---

### 0. github秘钥相关设置

### 1.下载安装nodejs

<!--more-->

在[node.js中文网](http://nodejs.cn/)下载并安装

### 2. clone自己的hexo工程，并切到分支

### 3. npm安装

在hexo工程下安装

- npm install hexo-cli -g           // 初始化 hexo

- **npm install**               // 重要命令.进入blog目录下运行。安装hexo server什么的

至此就OK了。下面是备用命令，有可能需要用到:

- npm install hexo --save

- npm install hexo-server --save            // 需要安装hexo-server，否则没有hexo server命令

- npm i hexo-generator-json-content --save  // 所有文章需要

一些插件
- npm install --save hexo-filter-flowchart  // markdown 流程图插件
