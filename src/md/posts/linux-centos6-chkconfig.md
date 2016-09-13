<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 chkconfig开机启动
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 chkconfig开机启动 chkconfig --list chkconfig --list name
-->

## chkconfig查看
```
chkconfig --list
chkconfig --list name
```
## chkconfig添加
```
chkconfig --add
chkconfig --add name
```

## chkconfig删除
```
chkconfig --del
chkconfig --del name
```

## chkconfig设置
```
chkconfig name on/off

#最后 还有个 --level 应该是对 0-6 某些 等级进行单独设置
chkconfig --level 35 name on/off

chkconfig httpd on
chkconfig --level 35 httpd on 将3和5 设置成on
```

## chkconfig等级
```
 等级0表示：表示关机
 等级1表示：单用户模式
 等级2表示：无网络连接的多用户命令行模式
 等级3表示：有网络连接的多用户命令行模式
 等级4表示：不可用
 等级5表示：带图形界面的多用户模式
 等级6表示：重新启动
```

## chkconfig使用
```
service name start
service name stop
service name status
service name restart
```

vi /etc/rc.d/rc.local
    service vsftpd start
    service iptables stop

cd /etc/init.d
cd /etc/rc.d/init.d

vi wang
```
    #!/bin/sh
    #add for chkconfig
    #chkconfig: 2345 70 30
    #description: the description of the shell
    #processname: wang
    activemq start
    start-dfs.sh
    start-yarn.sh
    rm -rf /opt/dev/oozie/oozie-4.0.0-cdh5.2.0/logs/*
    rm -rf /opt/dev/oozie/oozie-4.0.0-cdh5.2.0/oozie-server/temp/*
    oozied.sh start
```
```
chmod +x wang.sh
chkconfig --add wang

2345是指脚本的运行级别，即在2345这4种模式下都可以运行，234都是文本界面，5就是图形界面X
70是指脚本将来的启动顺序号，如果别的程序的启动顺序号比70小（比如44、45），则脚本需要等这些程序都启动以后才启动。
30是指系统关闭时，脚本的停止顺序号。
```

```
linux 下shell脚本执行多个命令的方法 
1.每个命令之间用;隔开
说明：各命令的执行给果，不会影响其它命令的执行。换句话说，各个命令都会执行，但不保证每个命令都执行成功。
2.每个命令之间用&&隔开
说明：若前面的命令执行成功，才会去执行后面的命令。这样可以保证所有的命令执行完毕后，执行过程都是成功的。
3.每个命令之间用||隔开
说明：||是或的意思，只有前面的命令执行失败后才去执行下一条命令，直到执行成功一条命令为止
```


 


 




