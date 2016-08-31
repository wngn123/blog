<!--
author: wngn123
head: head.png
date: 2016-08-28
title: php7.0.10安装
tags: php php7
category: PHP
status: publish
summary: php7.0.10安装,centos7源码安装php7.0.10.
-->

## php7.0.10安装

#### 依赖安装

```shell
yum -y install libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel pcre-devel
yum -y install curl-devel libmcrypt libmcrypt-devel mcrypt mhash libxslt-devel
```

#### 进入Linux源码目录下载源码并安装
```shell
cd /usr/local/src
wget  http://cn2.php.net/distributions/php-7.0.10.tar.gz
tar -zxvf php-7.0.10.tar.gz
cd php-7.0.10

./configure --prefix=/usr/local/php7 --exec-prefix=/usr/local/php7 --bindir=/usr/local/php7/bin --sbindir=/usr/local/php7/sbin --includedir=/usr/local/php7/include --libdir=/usr/local/php7/lib/php --mandir=/usr/local/php7/php/man --with-config-file-path=/usr/local/php7/etc --with-mysql-sock=/var/run/mysql/mysql.sock --with-mcrypt=/usr/include --with-mhash --with-openssl --with-mysql=shared,mysqlnd --with-mysqli=shared,mysqlnd --with-pdo-mysql=shared,mysqlnd --with-gd --with-iconv --with-zlib --enable-zip --enable-inline-optimization --disable-debug --disable-rpath --enable-shared --enable-xml --enable-bcmath --enable-shmop --enable-sysvsem --enable-mbregex --enable-mbstring --enable-ftp --enable-gd-native-ttf --enable-pcntl --enable-sockets --with-xmlrpc --enable-soap --without-pear --with-gettext --enable-session --with-curl --with-jpeg-dir --with-freetype-dir --enable-opcache --enable-fpm --enable-fastcgi --with-fpm-user=work --with-fpm-group=work --without-gdbm --disable-fileinfo

make &&  make install
```

#### 配置

```shell
#php配置
cp php.ini-production /usr/local/php/etc/php.ini  
cp /usr/local/php7/etc/php-fpm.conf.default /usr/local/php7/etc/php-fpm.conf
cp /usr/local/php7/etc/php-fpm.d/www.conf.default /usr/local/php7/etc/php-fpm.d/www.conf
cp ./sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
#查看配置文件
sed -e "s/;.*//g"  /usr/local/php7/etc/php.ini | awk '{if (length !=0) print \$0}'
sed -e "s/;.*//g"  /usr/local/php7/etc/php-fpm.d/www.conf   | awk '{if (length !=0) print \$0}'

#添加启动服务
chmod 755 /etc/init.d/php-fpm 或 chmod +x /etc/init.d/php-fpm
chkconfig --add php-fpm && chkconfig php-fpm on
chkconfig --level 35 php-fpm on

```

#### 启动

```shell
#验证配置文件
/usr/local/php7/sbin/php-fpm -t
/usr/local/php7/sbin/php-fpm -c /usr/local/php7/etc/php.ini -y /usr/local/php7/etc/php-fpm.conf -t

#服务启动
Usage: /etc/init.d/php-fpm {start|stop|force-quit|restart|reload|status}
service php-fpm start
service php-fpm stop
service php-fpm restart
service php-fpm reload
service php-fpm status

#命令启动
/usr/local/ph7/sbin/php-fpm
/usr/local/php7/bin/php-cgi -b 127.0.0.1:9000 -c /usr/local/php7/etc/php.ini
netstat -tnlp 

启动
/etc/init.d/php-fpm

```

#### 添加php的环境变量
```
echo -e '\nexport PATH=/usr/local/php7/bin:/usr/local/php7/sbin:\$PATH\n' >> /etc/profile && source /etc/profile
```
