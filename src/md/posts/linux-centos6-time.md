<!--
author: wngn123
head: head.png
date: 2016-09-03
title: CentOS6 更改时区和时间格式
tags: centos centos6 linux
category: Centos
status: publish
summary: CentOS6 更改时区和时间格式
-->

## 修改时区
所有的时区相关文件都存放在/usr/share/zoneinfo/下
```
ls -F /usr/share/zoneinfo/
```
根据所需的时区文件做软链接：

#### 中国大陆
```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
#### 马来西亚
```
ln -sf /usr/share/zoneinfo/Asia/Kuala_Lumpur /etc/localtime
# 或者
rm /etc/localtime
cp /usr/share/zoneinfo/Asia/Kuala_Lumpur /etc/localtime
```
然后顺便修改下/etc/sysconfig/clock中ZONE选项，不改也不影响使用。

通过date命令查看是否变化

2010年 07月 27日 星期二 02:52:14 MYT

表明已经改为马来的时区了。

```
date "+%Y-%m-%d %H:%M:%S" 
```



## 时间格式
#### 临时更改显示样式，当回话结束后恢复原来的样式
```
export TIME_STYLE='+%Y-%m-%d %H:%M:%S'
```

#### 永久改变显示样式，更改后的效果会保存下来
修改/etc/profile文件，在文件内容末尾加入
```
export TIME_STYLE='+%Y-%m-%d %H:%M:%S'
```
执行如下命令，使你修改后的/etc/profile文件配置内容生效
```
source /etc/profile
```
