<!--
author: wngn123
head: head.png
date: 2016-08-28
title: HTTPS带证书java访问
tags: java ssl client
category: HTTPS
status: publish
summary: HTTPS 带证书java访问，Java需要验证客户端证书和服务端证书，客户端证书为P12库，服务端为keystore库，如果服务端证书已经添加到JVM证书库中，则代码可省略服务端的证书验证。
-->

## HTTPS带证书java访问

1.Java需要验证客户端证书和服务端证书，客户端证书为P12库，服务端为keystore库，客户端库需要密码，服务端库不需要密码。

2.如果服务端证书已经添加到JVM证书库中，则代码可省略服务端的证书验证。


#### JAVA SSL
```java
package com.dianru.analysis.module.weixin;

import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.GeneralSecurityException;
import java.security.KeyStore;

import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.HttpsURLConnection;
import javax.net.ssl.KeyManagerFactory;
import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManagerFactory;

public class HttpsPost {
    /** 获得KeyStore.
     * 
     * @param keyStorePath
     *                        密钥库路径
     * @param password
     *                        密码
     * @return 密钥库
     * @throws Exception */
    public static KeyStore getKeyStore(String password, String keyStorePath) throws Exception {
        // 实例化密钥库
        KeyStore ks = KeyStore.getInstance("JKS");
        // 获得密钥库文件流
        FileInputStream is = new FileInputStream(keyStorePath);
        // 加载密钥库
        ks.load(is, password.toCharArray());
        // 关闭密钥库文件流
        is.close();
        return ks;
    }

    /** 获得SSLSocketFactory.
     * 
     * @param password
     *                        密码
     * @param keyStorePath
     *                        密钥库路径
     * @param trustStorePath
     *                        信任库路径
     * @return SSLSocketFactory
     * @throws Exception */
    public static SSLContext getSSLContext(String password, String keyStorePath, String trustStorePath)
            throws Exception {
        // 实例化密钥库
        KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
        // 获得密钥库
        KeyStore keyStore = getKeyStore(password, keyStorePath);
        // 初始化密钥工厂
        keyManagerFactory.init(keyStore, password.toCharArray());

        // 实例化信任库
        TrustManagerFactory trustManagerFactory = TrustManagerFactory
                .getInstance(TrustManagerFactory.getDefaultAlgorithm());
        // 获得信任库
        KeyStore trustStore = getKeyStore(password, trustStorePath);
        // 初始化信任库
        trustManagerFactory.init(trustStore);
        // 实例化SSL上下文
        SSLContext ctx = SSLContext.getInstance("TLS");
        // 初始化SSL上下文
        ctx.init(keyManagerFactory.getKeyManagers(), trustManagerFactory.getTrustManagers(), null);
        // 获得SSLSocketFactory
        return ctx;
    }

    /** 初始化HttpsURLConnection.
     * 
     * @param password
     *                        密码
     * @param keyStorePath
     *                        密钥库路径
     * @param trustStorePath
     *                        信任库路径
     * @throws Exception */
    public static void initHttpsURLConnection(String password, String keyStorePath, String trustStorePath)
            throws Exception {
        // 声明SSL上下文
        SSLContext sslContext = null;
        // 实例化主机名验证接口
        HostnameVerifier hnv = new MyHostnameVerifier();
        try {
            sslContext = getSSLContext(password, keyStorePath, trustStorePath);
        } catch (GeneralSecurityException e) {
            e.printStackTrace();
        }
        if (sslContext != null) {
            HttpsURLConnection.setDefaultSSLSocketFactory(sslContext.getSocketFactory());
        }
        HttpsURLConnection.setDefaultHostnameVerifier(hnv);
    }

    /** 发送请求.
     * 
     * @param httpsUrl
     *                        请求的地址
     * @param xmlStr
     *                        请求的数据 */
    public static void post(String httpsUrl, String xmlStr) {
        HttpsURLConnection urlCon = null;
        try {
            urlCon = (HttpsURLConnection) (new URL(httpsUrl)).openConnection();
            urlCon.setDoInput(true);
            urlCon.setDoOutput(true);
            urlCon.setRequestMethod("POST");
            urlCon.setRequestProperty("Content-Length", String.valueOf(xmlStr.getBytes().length));
            urlCon.setUseCaches(false);
            //设置为gbk可以解决服务器接收时读取的数据中文乱码问题
            urlCon.getOutputStream().write(xmlStr.getBytes("gbk"));
            urlCon.getOutputStream().flush();
            urlCon.getOutputStream().close();
            BufferedReader in = new BufferedReader(new InputStreamReader(urlCon.getInputStream()));
            String line;
            while ((line = in.readLine()) != null) {
                System.out.println(line);
            }
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /** 测试方法.
     * 
     * @param args
     * @throws Exception */
    public static void main(String[] args) throws Exception {
        // 密码
        String password = "wanggang";
        // 密钥库
        String keyStorePath = "D:/wngn/cert/wngn/java-client.p12";
        // 信任库
        String trustStorePath = "D:/wngn/cert/wngn/java-server.truststore";
        // 本地起的https服务
        String httpsUrl = "https://java.vfou.com:8086/data/active/getLatest";
        // 传输文本
        String xmlStr = "";
        HttpsPost.initHttpsURLConnection(password, keyStorePath, trustStorePath);
        // 发起请求
        HttpsPost.post(httpsUrl, xmlStr);
    }
}

```

```java
package com.dianru.analysis.module.weixin;

import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.SSLSession;

/**
 * 实现用于主机名验证的基接口。 
 * 在握手期间，如果 URL 的主机名和服务器的标识主机名不匹配，则验证机制可以回调此接口的实现程序来确定是否应该允许此连接。
 */
public class MyHostnameVerifier implements HostnameVerifier {
    @Override
    public boolean verify(String hostname, SSLSession session) {
        if("localhost".equals(hostname)){
            return true;
        } else {
            return false;
        }
    }
}

```
#### HTTP CLIENT
```java
package com.dianru.analysis.module.weixin;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.security.KeyManagementException;
import java.security.KeyStore;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.security.UnrecoverableKeyException;
import java.security.cert.CertificateException;

import javax.net.ssl.SSLContext;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.conn.ssl.SSLConnectionSocketFactory;
import org.apache.http.conn.ssl.SSLContexts;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

/** User: rizenguo Date: 2014/10/29 Time: 14:36 */
public class HttpsRequest {

    public static Logger LOG = LogManager.getLogger(HttpsRequest.class);

    public interface ResultListener {
        public void onConnectionPoolTimeoutError();
    }

    // 表示请求器是否已经做了初始化工作
    private boolean hasInit = false;
    // 请求器的配置
    private RequestConfig requestConfig;

    // HTTP请求器
    private CloseableHttpClient httpClient;

    public HttpsRequest() {
        try {
            init();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void init() throws IOException, KeyStoreException, UnrecoverableKeyException, NoSuchAlgorithmException,
            KeyManagementException {

        KeyStore keyStore = KeyStore.getInstance("PKCS12");
        FileInputStream instream = new FileInputStream(new File("D:/wngn/cert/wngn/java-client.p12"));

        KeyStore trustStore = KeyStore.getInstance(KeyStore.getDefaultType());
        FileInputStream instream2 = new FileInputStream(new File("D:/wngn/cert/wngn/java-server.truststore"));

        try {
            keyStore.load(instream, "wanggang".toCharArray());// 设置证书密码
            trustStore.load(instream2, "wanggang".toCharArray());// 设置证书密码
        } catch (CertificateException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } finally {
            instream.close();
        }
        // Trust own CA and all self-signed certs 相信自己的CA和所有自签名的证书 
        SSLContext sslcontext = SSLContexts.custom().loadKeyMaterial(keyStore, "wanggang".toCharArray())
                .loadTrustMaterial(trustStore).build();
        // Allow TLSv1 protocol only  只允许使用TLSv1协议 
        SSLConnectionSocketFactory sslsf = new SSLConnectionSocketFactory(sslcontext, new String[] { "TLSv1" }, null,
                SSLConnectionSocketFactory.BROWSER_COMPATIBLE_HOSTNAME_VERIFIER);
        httpClient = HttpClients.custom().setSSLSocketFactory(sslsf).build();
        // 根据默认超时限制初始化requestConfig
        requestConfig = RequestConfig.custom().setSocketTimeout(10 * 1000).setConnectTimeout(30 * 1000).build();
        hasInit = true;
    }

    public String sendPost(String url) throws UnrecoverableKeyException, KeyManagementException,
            NoSuchAlgorithmException, KeyStoreException, IOException {
        if (!hasInit) {
            init();
        }
        String result = null;
        HttpPost httpPost = new HttpPost(url);
        System.out.println("API，POST过去的数据是：");
        // 得指明使用UTF-8编码，否则到API服务器XML的中文不能被成功识别
        StringEntity postEntity = new StringEntity("", "UTF-8");
        httpPost.addHeader("Content-Type", "text/xml");
        httpPost.setEntity(postEntity);
        // 设置请求器的配置
        httpPost.setConfig(requestConfig);

        try {
            HttpResponse response = httpClient.execute(httpPost);
            HttpEntity entity = response.getEntity();
            result = EntityUtils.toString(entity, "UTF-8");
            System.out.println(result);
        } catch (Exception e) {
            LOG.error(url + "-" + e.getMessage());
            e.printStackTrace();
        } finally {
            httpPost.abort();
        }
        return result;
    }

    private static final String HTTPS_URL = "https://java.vfou.com:8086/data/active/getLatest";

    public static void main(String[] args) throws UnrecoverableKeyException, KeyManagementException,
            NoSuchAlgorithmException, KeyStoreException, IOException {
        HttpsRequest CLIENT = new HttpsRequest();
        String response = CLIENT.sendPost(HTTPS_URL);
        System.out.println(response);
    }
}

```