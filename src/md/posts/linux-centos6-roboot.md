<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 关机与重启
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 关机与重启
-->


## Linux centos重启命令
```
reboot
shutdown -r now 立刻重启(root用户使用)
shutdown -r 10 过10分钟自动重启(root用户使用)
shutdown -r 20:35 在时间为20:35时候重启(root用户使用)
shutdown -c 取消重启
```

## Linux centos关机命令
```
halt 立刻关机
poweroff 立刻关机
shutdown -h now 立刻关机(root用户使用)
shutdown -h 10 10分钟后自动关机
```