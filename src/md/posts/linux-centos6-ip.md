<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 CentOS修改ip
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 CentOS修改ip
-->

## 临时修改（重启失效）
```
ifconfig eth0 192.168.0.105
```
后面接IP地址， 网络掩码和 网关，如果不设置，就使用默认的掩码

## 永久修改
```
vi /etc/sysconfig/network-scripts/ifcfg-eth0 
```
```
DEVICE="eth0"
BOOTPROTO="static"
BROADCAST="192.168.0.255"
DNS1="192.168.0.1"
GATEWAY="192.168.0.1"
HWADDR="00:0C:29:71:EC:09"
IPADDR="192.168.0.105"
IPV6INIT="yes"
NETMAST="255.255.255.0"
NM_CONTROLLED="yes"
ONBOOT="yes"
TYPE="Ethernet"
UUID="bb249954-ad15-468e-9604-752ce43d641b"
```

```
DEVICE=eth0                     网卡对应的设备别名，如ifcfg-eth0第一块网卡 
BOOTPROTO=static                网卡获得ip地址的方式，static（静态 ip地址）
                                                     dhcp（通过dhcp协议获取ip）
                                                     bootip通过bootp协议获得的ip地址 
BROADCAST=192.168.0.255         子网广播地址 
HWADDR=00:50:56:8E:47:EE        网卡物理地址 
IPADDR=192.168.0.105            网卡IP地址
IPV6INIT=no                     是否启用IPV6 IPV6_AUTOCONF=no 
NETMASK=255.255.255.0           网卡对应网络掩码 
NETWORK=192.168.1.0             网卡对应的网络地址 
ONBOOT=yes                      系统启动时是否设置此网络接口，设置为yes时，系统启动时激活此设备。默认设置为yes
```

## 重启网络服务
```
service network restart 
# 或
/etc/init.d/network restart
```