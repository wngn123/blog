<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 修改环境变量
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 修改环境变量
-->

## 修改系统环境变量
```
vi /etc/profile
```

```
export JAVA_HOME=/opt/dev/jdk/jdk1.7.0_67
export PATH=$PATH:$JAVA_HOME/bin
```
## 修改用户环境变量
```
vi /root/.bash_profile
vi /home/user/.bash_profile
vi ~/.bash_profile
```

```
export JAVA_HOME=/opt/dev/jdk/jdk1.7.0_67
export PATH=$PATH:$JAVA_HOME/bin
```

