<!--
author: wngn123
head: head.png
date: 2016-08-28
title: HTTPS 证书keytool
tags: keytool
category: HTTPS
status: publish
summary: HTTPS 证书keytool
-->

## 证书keytool

* -genkeypair：生成一对非对称密钥;
+ -alias：指定密钥对的别名，该别名是公开的;
- -keyalg：指定加密算法，本例中的采用通用的RAS加密算法;
* -keystore:密钥库的路径及名称，不指定的话，默认在操作系统的用户目录下生成一个".keystore"的文件
* -validity 365”含义是证书有效期，365表示1年，默认值是90天
* -keypass 密钥密码
* -storepass 密钥库密码

** 注意keypass和storepass保持一致 **

* **证书系统：** 证书系统管理很多证书库（keystore）
* **证书仓库：**证书仓库管理很多证书条目（alias）
* **证书条目：**证书条目即一条证书

#### 证书管理
```
创建
keytool -genkey -alias "name" -keyalg RSA keystore "test.keystore" -storepass password
显示
keytool -printcert -file "test.crt"   
列表
keytool -list -keystore test.keystore [-v -alias name]
导出
keytool -export -alias name -file test.cer -keystore test.keystore
导入
keytool -import -keystore test_cacerts -file test.cer
删除：
keytool -delete -keystore test.keystore -alias name
修改密码
keytool -keypasswd -alias name -keystore test.keystore
keytool -keypasswd -alias name -keypass testtesttest1 -new testtest1 -storepass testtest -keystore test.keystore  
```

#### jdk证书

为服务器生成证书
```
keytool -list -keystore %JAVA_HOME%/jre/lib/security/cacerts -v  -alias name
keytool -export -file test.cer -keystore %JAVA_HOME%/jre/lib/security/cacerts -alias name
keytool -import -file test.cer -keystore %JAVA_HOME%/jre/lib/security/cacerts -alias name
keytool -delete -keystore %JAVA_HOME%/jre/lib/security/cacerts -alias name
```

#### window实例：
证书库路径：D:/wngn/cert/wngn/wngn.keystore
证书别名：wngn-server wngn-client-1 wngn-client-2 wngn-client-n

```
What is your first and last name?
  [Unknown]:  www.wngn.com
What is the name of your organizational unit?
  [Unknown]:  wngn.com
What is the name of your organization?
  [Unknown]:  wngn
What is the name of your City or Locality?
  [Unknown]:  BJ
What is the name of your State or Province?
  [Unknown]:  BJ
What is the two-letter country code for this unit?
  [Unknown]:  CH
Is CN=java.vfou.com, OU=vfou.com, O=vfou, L=BJ, ST=BJ, C=CH correct?
  [no]:  y

keytool -genkey -alias wngn-server -keyalg RSA -keysize 1024 -keypass wanggang -validity 365 -keystore D:/wngn/cert/wngn/wngn-server.keystore -dname "CN=www.wngn.com,OU=wngn.com,O=wngn,L=BJ,ST=BJ,C=CH" -storepass wanggang -keypass wanggang
```


#### 遇到错误 

输入keystore密码：

keytool错误： java.io.IOException: Keystore was tampered with, or password was incorrect 

对于很多服务器比如glassfish或者tomcat之类的，在证书过期，我们需要删除时，需要输入保护密码，默认的就是changeit，输入这个密码就可以了。 




