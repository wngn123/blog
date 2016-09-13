<!--
author: wngn123
head: head.png
date: 2016-09-13
title: JVM工具 jps
tags: java jvm jps
category: Java
status: publish
summary: JVM工具 jps 用来查看所有的jvm进程，包括进程ID，进程启动的路径等
-->

## 介绍
用来查看所有的jvm进程，包括进程ID，进程启动的路径等。

## 语法

```
jps -help
usage: jps [-help]
       jps [-q] [-mlvV] [<hostid>]

Definitions:
    <hostid>:      <hostname>[:<port>]
```

## jps
默认显示pid和class名称
```
[work@wanggang oom] jps
6005 Jps
5773 OOMServer
```

## -q参数
只显示pid，不显示class名称,jar文件名和传递给main 方法的参数
```
[work@wanggang oom] jps -q
6028
5773
```
## -m参数
输出传递给main 方法的参数，在嵌入式jvm上可能是null
```
[work@wanggang oom] jps -m
6043 Jps -m
5773 OOMServer
```
## -l参数
输出应用程序main class的完整package名 或者 应用程序的jar文件完整路径名
```
[work@wanggang oom] jps -l
6149 sun.tools.jps.Jps
5773 com.zzia.wngn.oom.OOMServer
```
## -v参数(小写v)
输出传递给JVM的参数
```
[work@wanggang oom] jps -v
6164 Jps -Dapplication.home=/opt/dev/jdk/jdk1.8.0_91 -Xms8m
5773 OOMServer -Xmx1024m -Xms512m -XX:+UseConcMarkSweepGC
```
## -V参数(大写V)
输出通过文件传递给JVM的参数（如.hotspotrc或通过-XX:Flags=<filename>指定的文件）
```
[work@wanggang oom] jps -V
6187 Jps
5773 OOMServer
```

## 参数合并使用
参数mlvV可以合并一起使用，但是q不能和其他参数一起使用
```
[work@wanggang oom] jps -mlv
6202 sun.tools.jps.Jps -mlv -Dapplication.home=/opt/dev/jdk/jdk1.8.0_91 -Xms8m
5773 com.zzia.wngn.oom.OOMServer -Xmx1024m -Xms512m -XX:+UseConcMarkSweepGC
```