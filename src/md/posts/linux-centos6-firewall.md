<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 firewall防火墙
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 firewall防火墙 service iptables start|stop|restart|status
-->

## 启动防火墙
```
service iptables start
service iptables restart
```
## 关闭防火墙
```
service iptables stop
```
## 查看防火墙
```
service iptables status
whereis iptables
sudo iptables -L
sudo iptables -L -n --line-numbers 
```
## 设置防火墙
```
iptables -P INPUT DROP
iptables -P OUTPUT ACCEPT
iptables -P FORWARD DROP

#ssh端口，开启目标端口
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
#http端口
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
#ftp端口
iptables -A INPUT -p tcp --dport 21 -j ACCEPT
iptables -A INPUT -p tcp --dport 20 -j ACCEPT
#svn端口
iptables -A INPUT -p tcp --dport 9999 -j ACCEPT
#tomcat端口
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
iptables -A INPUT -p tcp --dport 8081 -j ACCEPT
iptables -A INPUT -p tcp --dport 8086 -j ACCEPT
iptables -A INPUT -p tcp --dport 8085 -j ACCEPT

iptables -A INPUT -p icmp -j ACCEPT
iptables -A OUTPUT -p icmp -j ACCEPT

iptables -A INPUT -i lo -p all -j ACCEPT
iptables -A OUTPUT -o lo -p all -j ACCEPT

iptables -A OUTPUT -p tcp --sport 31337 -j DROP
iptables -A OUTPUT -p tcp --dport 31337 -j DROP

#对外ping
iptables -A INPUT -p icmp --icmp-type echo-reply -j ACCEPT
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```
#### 端口开放
```
# 允许8081端口访问
iptables -A INPUT -p tcp --dport 8081 -j ACCEPT
```
#### 端口转发
```
#8101端口转发到80端口，tomcat用
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8101
```
#### 端口测试
```
ping ip
telnet ip port

ping 192.168.0.105
telnet 192.168.0.105 8081
```
#### 允许指定IP登录
```
#允许指定IP登录
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -s 172.24.94.214 -p tcp --dport 22 -j ACCEPT
iptables -D INPUT -s 172.24.94.214 -p tcp --dport 22 -j ACCEPT
```

