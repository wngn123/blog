<!--
author: wngn123
head: head.png
date: 2016-09-13
title: JVM工具 jmap
tags: java jvm jmap
category: Java
status: publish
summary: JVM工具 jmap
-->

## 简介

打印出某个java进程（使用pid）内存内的，所有‘对象’的情况（如：产生那些对象，及其数量）。

可以输出所有内存中对象的工具，甚至可以将JVM中的heap，以二进制输出成文本。使用方法`jmap -histo pid`。如果连用`SHELL jmap -histo pid>a.log`可以将其保存到文本中去，在一段时间后，使用文本对比工具，可以对比出GC回收了哪些对象。`jmap -dump:format=b,file=outfile 3024`可以将3024进程的内存heap输出出来到outfile文件里，再配合MAT（内存分析工具(Memory Analysis Tool），使用参见：[http://blog.csdn.net/fenglibing/archive/2011/04/02/6298326.aspx](http://blog.csdn.net/fenglibing/archive/2011/04/02/6298326.aspx) 或与jhat (Java Heap Analysis Tool)一起使用，能够以图像的形式直观的展示当前内存是否有问题。

64位机上使用需要使用如下方式：
```
jmap -J-d64 -heap pid
```
## 语法
``` 
Usage:
    jmap [option] <pid>
        (to connect to running process)
    jmap [option] <executable <core>
        (to connect to a core file)
    jmap [option] [server_id@]<remote server IP or hostname>
        (to connect to remote debug server)

where <option> is one of:
    <none>               to print same info as Solaris pmap
    -heap                to print java heap summary
    -histo[:live]        to print histogram of java object heap; if the "live"
                         suboption is specified, only count live objects
    -clstats             to print class loader statistics
    -finalizerinfo       to print information on objects awaiting finalization
    -dump:<dump-options> to dump java heap in hprof binary format
                         dump-options:
                           live         dump only live objects; if not specified,
                                        all objects in the heap are dumped.
                           format=b     binary format
                           file=<file>  dump heap to <file>
                         Example: jmap -dump:live,format=b,file=heap.bin <pid>
    -F                   force. Use with -dump:<dump-options> <pid> or -histo
                         to force a heap dump or histogram when <pid> does not
                         respond. The "live" suboption is not supported
                         in this mode.
    -h | -help           to print this help message
    -J<flag>             to pass <flag> directly to the runtime system
  
```

## jmap –heap pid
```
[work@wanggang oom] jmap -heap 5773
Attaching to process ID 5773, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.91-b14

using parallel threads in the new generation. ##新生代采用的是并行线程处理方式
using thread-local object allocation.
Concurrent Mark-Sweep GC ##同步并行垃圾回收

Heap Configuration: ##堆配置情况
   MinHeapFreeRatio         = 40  ##最小堆使用比例
   MaxHeapFreeRatio         = 70 ##最大堆可用比例
   MaxHeapSize              = 1073741824 (1024.0MB) ##最大堆空间大小
   NewSize                  = 348913664 (332.75MB)  ##新生代分配大小	
   MaxNewSize               = 348913664 (332.75MB) ##最大可新生代分配大小
   OldSize                  = 187957248 (179.25MB) ##老生代大小
   NewRatio                 = 2 ##新生代比例
   SurvivorRatio            = 8 ##新生代与suvivor的比例
   MetaspaceSize            = 21807104 (20.796875MB) ##perm区大小	
   CompressedClassSpaceSize = 1073741824 (1024.0MB) 
   MaxMetaspaceSize         = 17592186044415 MB ##最大可分配perm区大小
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:##堆使用情况
New Generation (Eden + 1 Survivor Space):##新生代（伊甸区 + survior空间）
   capacity = 314048512 (299.5MB)  ##伊甸区容量
   used     = 50253216 (47.925201416015625MB) ##已经使用大小
   free     = 263795296 (251.57479858398438MB) ##剩余容量
   16.001736699838272% used  ##使用比例
Eden Space:##伊甸区
   capacity = 279183360 (266.25MB)  ##伊甸区容量
   used     = 50253216 (47.925201416015625MB) ##伊甸区使用
   free     = 228930144 (218.32479858398438MB) #伊甸区当前剩余容量
   18.000075649207748% used ##伊甸区使用情况
From Space: ##survior1区
   capacity = 34865152 (33.25MB) ##survior1区容量
   used     = 0 (0.0MB) ##surviror1区已使用情况
   free     = 34865152 (33.25MB) ##surviror1区剩余容量
   0.0% used ##survior1区使用比例
To Space:  ##survior2 区
   capacity = 34865152 (33.25MB) ##survior2区容量
   used     = 0 (0.0MB) ##survior2区已使用情况
   free     = 34865152 (33.25MB)##survior2区剩余容量
   0.0% used ## survior2区使用比例
concurrent mark-sweep generation: ##老生代使用情况
   capacity = 187957248 (179.25MB) ##老生代容量
   used     = 0 (0.0MB)##老生代已使用容量
   free     = 187957248 (179.25MB)##老生代剩余容量
   0.0% used ##老生代使用比例

3589 interned Strings occupying 288776 bytes.

```


