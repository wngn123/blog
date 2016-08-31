<!--
author: wangn123
head: head.png
date: 2016-08-29
title: GitBlog和GitHub同步
tags: git blog
category: GitBlog
status: publish
summary: GitBlog部署本地虚拟机服务器上，在GitHub上通过GotPage功能展示静态网页，每次更新博客将PHP程序静态导出，同步到GitHub仓库中。
-->

## 一. 简介 ##
GitBlog部署本地虚拟机服务器上，在GitHub上通过GotPage功能展示静态网页，每次更新博客将PHP程序静态导出，同步到GitHub仓库中。

## 二. 导出静态文件 ##

GitBlog支持把整个博客网站导出为静态HTML文件，这样导出整个网站后，可以把它上传到网站空间，以静态形式访问，导出的后的网站结构和运行在PHP环境中一样。

你可以使用以下命令静态导出网站：
```shell
/usr/local/php5/bin/php /home/work/prod/php/gitblog/index.php Gitblog exportSite
```
以上命令请换成你的网站路径。成功导出后，会在GitBlog目录下生成一个_site的文件夹，所有导出的静态资源都在这里，你可以随意复制它布署到你的环境中。

导出前可清除cache目录中的缓存，以便导出最新的资源。
```shell
sudo rm -rf /home/work/prod/php/gitblog/app/cache/*
```

## 三. GitHub同步 ##

```shell
cd /home/work
git clone https://github.com/wngn123/wngn123.github.io.git
cd wngn123.github.io
rm -rf /home/work/wngn123.github.io/*
cp -rf /home/work/prod/php/gitblog/_site/* /home/work/wngn123.github.io
git status
git add --all
git commit -m 'gitblog 2016-08-29'
git push origin master
```

## 四. 清理数据 ##
```shell
cd /home/work
rm -rf /home/work/wngn123.github.io
rm -rf /home/work/prod/php/gitblog/_site
```

