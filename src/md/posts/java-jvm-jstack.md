<!--
author: wngn123
head: head.png
date: 2016-09-13
title: JVM工具 jstack
tags: java jvm jstack
category: Java
status: publish
summary: JVM工具 jstack 主要用来查看某个Java进程内的线程堆栈信息
-->

## 简介

jstack用于打印出给定的java进程ID或core file或远程调试服务的Java堆栈信息，如果是在64位机器上，需要指定选项`-J-d64`，Windows的jstack使用方式只支持以下的这种方式`jstack [-l] pid`

如果java程序崩溃生成core文件，jstack工具可以用来获得core文件的java stack和native stack的信息，从而可以轻松地知道java程序是如何崩溃和在程序何处发生问题。另外，jstack工具还可以附属到正在运行的java程序中，看到当时运行的java程序的java stack和native stack的信息, 如果现在运行的java程序呈现hung的状态，jstack是非常有用的

## 语法
``` 
Usage:
    jstack [-l] <pid>
        (to connect to running process)
    jstack -F [-m] [-l] <pid>
        (to connect to a hung process)
    jstack [-m] [-l] <executable> <core>
        (to connect to a core file)
    jstack [-m] [-l] [server_id@]<remote server IP or hostname>
        (to connect to a remote debug server)

Options:
    -F  to force a thread dump. Use when jstack <pid> does not respond (process is hung)
    -m  to print both java and native frames (mixed mode)
    -l  long listing. Prints additional information about locks
    -h or -help to print this help message
```

`core` 将被打印信息的core dump文件

`remote-hostname-or-IP` 远程debug服务的主机名或ip

`server-id` 唯一id,假如一台主机上多个远程debug服务 


`-F` 当`jstack [-l] pid`没有相应的时候强制打印栈信息

`-l`长列表. 打印关于锁的附加信息,例如属于java.util.concurrent的ownable synchronizers列表.

`-m`打印java和native c/c++框架的所有栈信息.

`-h | -help`打印帮助信息

`pid` 需要被打印配置信息的java进程id,可以用jps查询.

## 使用
jstack可以定位到线程堆栈，根据堆栈信息我们可以定位到具体代码，所以它在JVM性能调优中使用得非常多。下面我们来一个实例找出某个Java进程中最耗费CPU的Java线程并定位堆栈信息，用到的命令有ps、top、printf、jstack、grep

 第一步先找出Java进程ID，我部署在服务器上的Java应用名称为mrf-center：
`ps -ef | grep mrf-center | grep -v grep`

第二步找出该进程内最耗费CPU的线程，可以使用`ps -Lfp pid`或者`ps -mp pid -o THREAD, tid, time`或者`top -Hp pid`，我这里用第三个, TIME列就是各个Java线程耗费的CPU时间

`printf "%x\n" 21742 `得到21742的十六进制值为54ee

jstack用来输出进程21711的堆栈信息，然后根据线程ID的十六进制值grep

`stack 21711 | grep 54ee`

#### 实例
```jstack -F 8339```
```
Attaching to process ID 8339, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.91-b14
Deadlock Detection:

No deadlocks found.

Thread 8357: (state = BLOCKED)
 - java.lang.Thread.sleep(long) @bci=0 (Compiled frame; information may be imprecise)
 - com.zzia.wngn.oom.task.MemTask.run() @bci=44, line=19 (Compiled frame)
 - java.util.concurrent.ThreadPoolExecutor.runWorker(java.util.concurrent.ThreadPoolExecutor$Worker) @bci=95, line=1142 (Interpreted frame)
 - java.util.concurrent.ThreadPoolExecutor$Worker.run() @bci=5, line=617 (Interpreted frame)
 - java.lang.Thread.run() @bci=11, line=745 (Interpreted frame)


Thread 8350: (state = BLOCKED)


Thread 8349: (state = BLOCKED)


Thread 8348: (state = BLOCKED)
 - java.lang.Object.wait(long) @bci=0 (Interpreted frame)
 - java.lang.ref.ReferenceQueue.remove(long) @bci=59, line=143 (Interpreted frame)
 - java.lang.ref.ReferenceQueue.remove() @bci=2, line=164 (Compiled frame)
 - java.lang.ref.Finalizer$FinalizerThread.run() @bci=36, line=209 (Interpreted frame)


Thread 8347: (state = BLOCKED)
 - java.lang.Object.wait(long) @bci=0 (Interpreted frame)
 - java.lang.Object.wait() @bci=2, line=502 (Compiled frame)
 - java.lang.ref.Reference.tryHandlePending(boolean) @bci=54, line=191 (Compiled frame)
 - java.lang.ref.Reference$ReferenceHandler.run() @bci=1, line=153 (Interpreted frame)


Thread 8340: (state = BLOCKED)
 - java.lang.Thread.sleep(long) @bci=0 (Compiled frame; information may be imprecise)
 - com.zzia.wngn.oom.OOMServer.main(java.lang.String[]) @bci=134, line=56 (Interpreted frame)

```