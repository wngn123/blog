<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 修改root管理员密码和用户操作
tags: centos
category: Centos
status: publish
summary: 修改root管理员密码和用户操作
-->


## 修改root密码
```
以root 身份登录(SSH操作)
输入 passwd 命令 就可以看到提示输入新密码了
```
## 用户组操作
#### 添加组
```
groupadd groupname
```
#### 修改组
```
groupmod -n groupname newname
```
#### 删除组
```
groupdel groupname
```
## 用户操作

#### 添加用户
```
useradd -g groupname username
gpasswd -a username groupname 从组中添加用户
```
#### 设置密码
```
passwd username
```

#### 删除用户
```
userdel username
usermod –g groupname username（强制删除该用户的主目录和主目录下的所有文件和子目录）
gpasswd -d username groupname 从组中删除用户
```

#### 修改用户
```
usermod -l username newname
usermod -g groupname username
```
#### 锁定用户
```
passwd username –l，usermod -l username 
```
#### 释放用户
```
passwd username –u，usermod -u username 
```
#### 查看权限
```
id
id user
```
