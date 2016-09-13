<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 sudo权限
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 sudo权限
-->

## linux给用户添加sudo权限

有时候，linux下面运行sudo命令，会提示类似： 

`xxxis not in the sudoers file.  This incident will be reported. `

这里，xxx是用户名称，然后导致无法执行sudo命令，这时候，如下解决：
进入超级用户模式。也就是输入"su -",系统会让你输入超级用户密码，输入密码后就进入了超级用户模式。（当然，你也可以直接用root用）
添加文件的写权限。也就是输入命令"chmod u+w /etc/sudoers"。 
编辑/etc/sudoers文件。也就是输入命令"vim /etc/sudoers",进入编辑模式，找到这一 行："root ALL=(ALL) ALL"在起下面添加"xxx ALL=(ALL) ALL"(这里的xxx是你的用户名)，然后保存退出。
撤销文件的写权限。也就是输入命令"chmod u-w /etc/sudoers"。 
然后就行了。

## 执行的shell命令

```shell
ll /etc/sudoers
sudo chmod u+w /etc/sudoers
sudo vi /etc/sudoers
    root ALL=(ALL) ALL
    user ALL=(ALL) ALL
sudo chmod u-w /etc/sudoers

# 如果sudo时不验证密码则使用下面配置：
wang ALL=(ALL) NOPASSWD: ALL
work ALL=(ALL) NOPASSWD: ALL
```