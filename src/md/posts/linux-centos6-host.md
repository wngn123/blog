<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 修改hosts和hostname
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 修改hosts和hostname
-->

## 修改hostname
```
cat /etc/sysconfig/network
vi /etc/sysconfig/network
```
```
NETWORKING=yes
HOSTNAME=wanggang
```

##  修改hosts
```
cat /etc/hosts
vi /etc/hosts
```

```
127.0.0.1 localhost

127.0.0.1 wanggang
192.168.0.100 window.wngn.zzia.com
127.0.0.1 linux.wngn.zzia.com
127.0.0.1 server
127.0.0.1 wngn.com
127.0.0.1 wngn.php.com

127.0.0.1 wngn.test.com
127.0.0.1 php.wngn.com
127.0.0.1 test.wngn.com
127.0.0.1 book.wngn.com
127.0.0.1 resource.wngn.com
127.0.0.1 sources.wngn.com
127.0.0.1 user.wngn.com
127.0.0.1 blog.wngn.com
127.0.0.1 crud.wngn.com

127.0.0.1 www.php.com
127.0.0.1 test.php.com
127.0.0.1 test.lua.com
127.0.0.1 user.lua.com
127.0.0.1 crud.lua.com
127.0.0.1 blog.lua.com
127.0.0.1 www.lua.com
127.0.0.1 echo.lua.com

127.0.0.1 www.blog.com
127.0.0.1 conf.lua.com
127.0.0.1 user.lua.com

```
