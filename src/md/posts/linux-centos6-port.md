<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 查看端口占用
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 查看端口占用
-->

## shell命令
```
    lsof -i tcp:80
    netstat -ntlp
    netstat -nlp | grep port
    netstat -anp|grep port
```
