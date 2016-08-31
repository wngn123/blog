<!--
author: wngn123
head: head.png
date: 2016-08-28
title: php5.6.25安装
tags: php php5
category: PHP
status: publish
summary: php5.6.25安装,centos7源码安装php5.6.25.
-->

## php5.6.25安装

#### 进入Linux源码目录下载源码并安装
```shell
cd /usr/local/src
tar -zxvf php-php-5.6.25.tar.gz
cd php-php-5.6.25

./configure --prefix=/usr/local/php5 --with-config-file-path=/usr/local/php5/etc --enable-mb --enable-bcmath --enable-mbstring --enable-sockets --with-curl --enable-ftp --enable-sockets --disable-ipv6 --with-gd --with-jpeg-dir --with-png-dir --with-freetype-dir --enable-gd-native-ttf --with-iconv-t --with-zlib --with-pdo-mysql=mysqlnd --with-mysqli=mysqlnd --with-mysql=mysqlnd --enable-dom --enable-xml --enable-fpm 

make &&  make install

```

#### 配置

```shell
cp php.ini-production /usr/local/php5/etc/php.ini
cp /usr/local/php5/etc/php-fpm.conf.default /usr/local/php5/etc/php-fpm.conf
cp /src/local/php5/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
#添加启动服务
chmod 755 /etc/init.d/php-fpm 或 chmod +x /etc/init.d/php-fpm
chkconfig --add php-fpm && chkconfig php-fpm on
chkconfig --level 35 php-fpm on

```

#### 启动

```shell
#验证配置文件
/usr/local/php5/sbin/php-fpm -t
/usr/local/php5/sbin/php-fpm -c /usr/local/php5/etc/php.ini -y /usr/local/php5/etc/php-fpm.conf -t

#服务启动
Usage: /etc/init.d/php-fpm {start|stop|force-quit|restart|reload|status}
service php-fpm start
service php-fpm stop
service php-fpm restart
service php-fpm reload
service php-fpm status

#命令启动
/usr/local/ph5/sbin/php-fpm
/usr/local/php5/bin/php-cgi -b 127.0.0.1:9000 -c /usr/local/php5/etc/php.ini
netstat -tnlp 
```



