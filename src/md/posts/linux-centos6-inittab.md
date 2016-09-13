<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 inittab启动方式
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 CentOS设置默认启动命令行(不启动图形界面)
-->

CentOS设置默认启动命令行(不启动图形界面),在centOS6 中的修改方法如下:

1.root登陆，免得老是sudo


2.打开/etc/inittab 文件

   ```
   vi /etc/inittab
   ```
   
3.在默认的 run level 设置中,可以看到第一行书写如:
```
id:5:initdefault
```
(默认的 run level 等级为 5,即图形界面)

4.将第一行的 5 修改为 3 即可。

5.保存文件后重启系统你就可以看见是启动的文本界面了。