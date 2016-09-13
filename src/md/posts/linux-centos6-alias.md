<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 alias别名
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 alias别名 查看命令别名：alias  设置命令别名：alias 别名='命令' 取消命令别名：unalias 别名
-->

```
查看命令别名：alias
设置命令别名：alias 别名='命令'
取消命令别名：unalias 别名
```

## 查看别名
```
alias
```
## 设置别名
```
alias name='command'

alias ll='ls --time-style "+%Y-%m-%d %H:%M:%S" -lht'
```
## 取消别名
```
unalias name

unalias ll
```

## 设置全局别名
```
sudo vi /etc/bash.bashrc
source /etc/bash.bashrc
sudo vi/etc/bashrc
source /etc/bashrc
```
## 默认别名
```
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias la='ls -la --color=auto'
alias lh='ls -lah --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias vi='vim'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
# 自定义的别名
alias datetime='date +"%Y-%m-%d %H:%M:%S"'
alias ll='ls --time-style "+%Y-%m-%d %H:%M:%S" -lht --color=auto'
```
