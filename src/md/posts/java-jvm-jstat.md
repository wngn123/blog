<!--
author: wngn123
head: head.png
date: 2016-09-13
title: JVM工具 jstat
tags: java jvm jstat
category: Java
status: publish
summary: JVM工具 jstat JVM统计监测工具
-->

## 简介
Jstat用于监控基于HotSpot的JVM，对其堆的使用情况进行实时的命令行的统计，使用jstat我们可以对指定的JVM做如下监控：
- 类的加载及卸载情况
- 查看新生代、老生代及持久代的容量及使用情况
- 查看新生代、老生代及持久代的垃圾收集情况，包括垃圾回收的次数及垃圾回收所占用的时间
- 查看新生代中Eden区及Survior区中容量及分配情况等

jstat工具特别强大，它有众多的可选项，通过提供多种不同的监控维度，使我们可以从不同的维度来了解到当前JVM堆的使用情况。详细查看堆内各个部分的使用量，使用的时候必须加上待统计的Java进程号，可选的不同维度参数以及可选的统计频率参数。

它主要是用来显示GC及PermGen相关的信息，如果对GC不怎么了解，先看这篇文章
[http://blog.csdn.net/fenglibing/archive/2011/04/13/6321453.aspx](http://blog.csdn.net/fenglibing/archive/2011/04/13/6321453.aspx)

## 语法
``` 
Usage: jstat -help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

Definitions:
  <option>      An option reported by the -options option
  <vmid>        Virtual Machine Identifier. A vmid takes the following form:
                     <lvmid>[@<hostname>[:<port>]]
                Where <lvmid> is the local vm identifier for the target
                Java virtual machine, typically a process id; <hostname> is
                the name of the host running the target Java virtual machine;
                and <port> is the port number for the rmiregistry on the
                target host. See the jvmstat documentation for a more complete
                description of the Virtual Machine Identifier.
  <lines>       Number of samples between header lines.
  <interval>    Sampling interval. The following forms are allowed:
                    <n>["ms"|"s"]
                Where <n> is an integer and the suffix specifies the units as 
                milliseconds("ms") or seconds("s"). The default units are "ms".
  <count>       Number of samples to take before terminating.
  -J<flag>      Pass <flag> directly to the runtime system.
  
```
`<option>`   选项，我们一般使用 -gcutil 查看gc情况

`<vmid>`     vmid 是Java虚拟机ID，在Linux/Unix系统上一般就是进程ID

`<lines>`    lines 行数

`<interval>` interval是监控时间间隔，单位为微妙，不提供就意味着单次输出

`<count>`    count是最大输出次数,不提供且监控时间间隔有值的话， 就无限打印


参数interval和count代表查询间隔和次数，如果省略这两个参数，说明只查询一次。假设需要每250毫秒查询一次进程5828垃圾收集状况，一共查询5次，那命令行如下

`jstat -gc 5828 `

选项option代表这用户希望查询的虚拟机信息，主要分为3类：类装载、垃圾收集和运行期编译状况，具体选项及作用如下：

- `–class` 监视类装载、卸载数量、总空间及类装载所耗费的时间,统计classloader的行为
- `–gc` 监视Java堆状况，包括Eden区、2个Survivor区、老年代、永久代等的容量,统计gc行为
- `–gccapacity` 监视内容与-gc基本相同，但输出主要关注Java堆各个区域使用到的最大和最小空间
- `–gcutil` 监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比
- `–gccause` 与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因
- `–gcnew` 监视新生代GC的状况
- `–gcnewcapacity` 监视内容与-gcnew基本相同，输出主要关注使用到的最大和最小空间
- `–gcold` 监视老年代GC的状况
- `–gcoldcapacity` 监视内容与——gcold基本相同，输出主要关注使用到的最大和最小空间
- `–gcpermcapacity` 输出永久代使用到的最大和最小空间
- `–compiler` 输出JIT编译器编译过的方法、耗时等信息,统计hotspot just-in-time编译器的行为
- `–printcompilation` 输出已经被JIT编译的方法

-h n 每n个样本，显示header一次
-t n 在第一列显示时间戳列，时间戳时从jvm启动开始计算

## 输出GC信息 jstat -gc

#### jstat -gc pid interval count

输出的是GC信息，采样时间间隔为250ms，采样数为4

`jstat -gc 21711 250 4`
```
S0C    S1C    S0U   S1U  EC      EU      OC       OU      PC       PU       YGC  YGCT   FGC FGCT   GCT   
192.0  192.0  64.0  0.0  6144.0  1854.9  32000.0  4111.6  55296.0  25472.7  702  0.431  3   0.218  0.649
192.0  192.0  64.0  0.0  6144.0  1972.2  32000.0  4111.6  55296.0  25472.7  702  0.431  3   0.218  0.649
192.0  192.0  64.0  0.0  6144.0  1972.2  32000.0  4111.6  55296.0  25472.7  702  0.431  3   0.218  0.649
192.0  192.0  64.0  0.0  6144.0  2109.7  32000.0  4111.6  55296.0  25472.7  702  0.431  3   0.218  0.649
```

```
堆内存 = 年轻代 + 年老代 + 永久代
年轻代 = Eden区 + 两个Survivor区（From和To）

S0C、S1C、S0U、S1U：Survivor 0/1区容量（Capacity）和使用量（Used）
EC、EU：Eden区容量和使用量
OC、OU：年老代容量和使用量
PC、PU：永久代容量和使用量
YGC、YGT：年轻代GC次数和GC耗时
FGC、FGCT：Full GC次数和Full GC耗时
GCT：GC总耗时


```


## 输出GC信息 jstat -gcutil

#### jstat -gcutil pid interval count

`jstat -gcutil 8339`
```
  S0     S1     E       O      M       CCS     YGC  YGCT    FGC  FGCT    GCT   
  0.00   0.00   44.09   0.00   17.27   19.75   0    0.000   0    0.000   0.000
```

```
S0  — Heap上的 Survivor space 0 区已使用空间的百分比
S1  — Heap上的 Survivor space 1 区已使用空间的百分比
E   — Heap上的 Eden space 区已使用空间的百分比
O   — Heap上的 Old space 区已使用空间的百分比
P   — Perm space 区已使用空间的百分比
YGC — 从应用程序启动到采样时发生 Young GC 的次数
YGCT– 从应用程序启动到采样时 Young GC 所用的时间(单位秒)
FGC — 从应用程序启动到采样时发生 Full GC 的次数
FGCT– 从应用程序启动到采样时 Full GC 所用的时间(单位秒)
GCT — 从应用程序启动到采样时用于垃圾回收的总时间(单位秒)
```
## 输出class信息

#### jstat -class pid
显示加载class的数量，及所占空间等信息。

```
[work@wanggang oom] jstat -class 8339
Loaded  Bytes    Unloaded  Bytes     Time   
1893    3556.3   0         0.0       3.36
```
```
Loaded 装载的类的数量
Bytes 装载类所占用的字节数
Unloaded 卸载类的数量
Bytes 卸载类的字节数
Time 装载和卸载类所花费的时间
```

## 显示VM实时编译的数量等信息 jstat -compiler

#### jstat -compiler pid

```
Compiled 编译任务执行数量
Failed 编译任务执行失败数量
Invalid 编译任务执行失效数量
Time 编译任务消耗时间
FailedType 最后一个编译失败任务的类型
FailedMethod 最后一个编译失败任务所在的类及方法
```
## 显示对象大小 jstat -gccapacity

显示VM内存中三代（young,old,perm）对象的使用和占用大小 

#### jstat -gccapacity pid
```
NGCMN 年轻代(young)中初始化(最小)的大小(字节)
NGCMX 年轻代(young)的最大容量 (字节)
NGC 年轻代(young)中当前的容量 (字节)
S0C 年轻代中第一个survivor（幸存区）的容量 (字节)
S1C 年轻代中第二个survivor（幸存区）的容量 (字节)
EC 年轻代中Eden（伊甸园）的容量 (字节)
OGCMN old代中初始化(最小)的大小 (字节)
OGCMX old代的最大容量(字节)
OGC old代当前新生成的容量 (字节)
OC Old代的容量 (字节)
PGCMN perm代中初始化(最小)的大小 (字节)
PGCMX perm代的最大容量 (字节)
PGC perm代当前新生成的容量 (字节)
PC Perm(持久代)的容量 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数
```
## 年轻代对象的信息 jstat -gcnew

#### jstat -gcnew pid

```
S0C 年轻代中第一个survivor（幸存区）的容量 (字节)
S1C 年轻代中第二个survivor（幸存区）的容量 (字节)
S0U 年轻代中第一个survivor（幸存区）目前已使用空间 (字节)
S1U 年轻代中第二个survivor（幸存区）目前已使用空间 (字节)
TT 持有次数限制
MTT 最大持有次数限制
EC 年轻代中Eden（伊甸园）的容量 (字节)
EU 年轻代中Eden（伊甸园）目前已使用空间 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
YGCT 从应用程序启动到采样时年轻代中gc所用时间(s)
```

## 年轻代对象的信息及其占用量 jstat -gcnewcapacity

#### jstat -gcnewcapacity pid

```
NGCMN 年轻代(young)中初始化(最小)的大小(字节)
NGCMX 年轻代(young)的最大容量 (字节)
NGC 年轻代(young)中当前的容量 (字节)
S0CMX 年轻代中第一个survivor（幸存区）的最大容量 (字节)
S0C 年轻代中第一个survivor（幸存区）的容量 (字节)
S1CMX 年轻代中第二个survivor（幸存区）的最大容量 (字节)
S1C 年轻代中第二个survivor（幸存区）的容量 (字节)
ECMX 年轻代中Eden（伊甸园）的最大容量 (字节)
EC 年轻代中Eden（伊甸园）的容量 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数
```

## old代对象的信息 jstat -gcold

#### jstat -gcold  pid
 
```
PC Perm(持久代)的容量 (字节)
PU Perm(持久代)目前已使用空间 (字节)
OC Old代的容量 (字节)
OU Old代目前已使用空间 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数
FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT 从应用程序启动到采样时gc用的总时间(s)
```
## old代对象的信息及其占用量 jstat -gcoldcapacity

#### jstat -gcoldcapacity  pid
```
OGCMN old代中初始化(最小)的大小 (字节)
OGCMX old代的最大容量(字节)
OGC old代当前新生成的容量 (字节)
OC Old代的容量 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数
FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT 从应用程序启动到采样时gc用的总时间(s)
```
## perm对象的信息及其占用量 jstat -gcpermcapacity

#### jstat -gcpermcapacity  pid

```
PGCMN perm代中初始化(最小)的大小 (字节)
PGCMX perm代的最大容量 (字节)
PGC perm代当前新生成的容量 (字节)
PC Perm(持久代)的容量 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数
FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT 从应用程序启动到采样时gc用的总时间(s)
```
## 当前VM执行的信息 jstat -printcompilation

#### jstat -printcompilation  pid
```
Compiled 编译任务的数目
Size 方法生成的字节码的大小
Type 编译类型
Method 类名和方法名用来标识编译的方法。类名使用/做为一个命名空间分隔符。方法名是给定类中的方法。上述格式是由-XX:+PrintComplation选项进行设置的
```
