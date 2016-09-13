<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 yum安装软件
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 yum安装软件
-->

## 安装gcc
```
yum -y install gcc
yum -y install gcc-c++
yum -y install make
```
```
-- 或者
yum group install "Development Tools"
 
-- 或者
yum install gcc gcc-c++ kernel-devel
```