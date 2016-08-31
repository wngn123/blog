<!--
author: wngn123
head: head.png
date: 2016-08-28
title: HTTPS 双向证书
tags: keytool https
category: HTTPS
status: publish
summary: HTTPS 双向证书
-->

## 双向证书

服务器(centos)：172.16.1.10 

127.0.0.1 localhost

127.0.0.1 java.vfou.com

本地主机(window)：172.16.1.101

127.0.0.1 localhost

172.16.1.10 java.vfou.com


###### 1.生成服务端证书：
```
keytool -genkey -v -alias java-server -keyalg RSA -keystore /home/java/cert/java/java-server.keystore -dname "CN=java.vfou.com,OU=vfou.com,O=vfou,L=BJ,ST=BJ,C=CH" -storepass wanggang -keypass wanggang 
```
###### 2.导出服务端证书：
```
keytool -export -v -alias java-server -keystore /home/java/cert/java/java-server.keystore -storepass wanggang -rfc -file /home/java/cert/java/java-server.cer
```

###### 3.生产客户端证书：
```
keytool -validity 365 -genkeypair -v -alias java-client -keyalg RSA -storetype PKCS12 -keystore D:/wngn/cert/wngn/java-client.p12 -dname "CN=client,OU=client,O=client,L=BJ,ST=BJ,c=CH" -storepass wanggang -keypass wanggang
```
###### 4.导出客户端证书：
```
keytool -export -v -alias java-client -keystore D:/wngn/cert/wngn/java-client.p12 -storetype PKCS12 -storepass wanggang -rfc -file D:/wngn/cert/wngn/java-client.cer
```

###### 5.服务端信任客户端：
```
keytool -import -v -alias java-client -file  /home/java/cert/java/java-client.cer -keystore /home/java/cert/java/java-server.keystore -storepass wanggang
```

###### 6.客户端信任服务端：
```
keytool -import -v -alias java-server -file D:/wngn/cert/wngn/java-server.cer -keystore D:/wngn/cert/wngn/java-server.truststore -storepass wanggang
```

###### 7.服务器tomcat配置：
```
<Connector port="8086" protocol="org.apache.coyote.http11.Http11Protocol"
               maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
               keystoreFile="/home/java/cert/java/java-server.keystore" keystorePass="wanggang"
               truststoreFile="/home/java/cert/java/java-server.keystore" truststorePass="wanggang"
               clientAuth="true" sslProtocol="TLS" />
```
```
clientAuth:设置是否双向验证，默认为false，设置为true代表双向验证
keystoreFile:服务器证书文件路径
keystorePass:服务器证书密码
truststoreFile:用来验证客户端证书的根证书，此例中就是服务器证书
truststorePass:根证书密码
```
###### 8.让服务器信任客户端证书
    
由于是双向SSL认证，服务器必须要信任客户端证书，因此，必须把客户端证书添加为服务器的信任认证。由于不能直接将PKCS12格式的证书库导入，必须先把客户端证书导出为一个单独的CER文件,然后再将CER文件导入到服务端证书库。

###### 9.让客户端信任服务器证书

由于是双向SSL认证，客户端也要验证服务器证书，因此，必须把服务器证书添加到浏览的“受信任的根证书颁发机构”。由于不能直接将keystore格式的证书库导入，必须先把服务器证书导出为一个单独的CER文件，然后再将CER文件导入到客户端证书库（windows双击证书安装）。双击tomcat.cer文件，按照提示安装证书，将证书填入到“受信任的根证书颁发机构”。

###### 10.让客户端安装客户端（P12）证书

浏览器访问必须安装客户端证书，必须将P12的证书库安装到，客户端不是只包含公钥的证书，必须是一个p12库导入才行，证书必须包含私钥才行

###### 11.JVM证书导入
```
keytool -import -keystore D:/dev/jdk/jdk1.7.0_79/jre/lib/security/cacerts -file D:/wngn/cert/wngn/java-server.crt -alias java-server -storepass changeit
```
