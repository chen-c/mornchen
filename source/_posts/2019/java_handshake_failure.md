---
title: java链接ssl alert received HANDSHAKE_FAILURE的一种解决方式
date: 2019-04-12 12:32:17
tags: 
- JAVA
- SSL
categories: 
- 开发
---

一句话版本：把jdk1.6升级到1.8。^_^

<!-- more -->

在一个项目中，内网应用通过squid代理访问对端互联网https协议地址时，报如下错误：
> javax.net.ssl.SSLHandshakeException: Received fatal alert: handshake_failure

看着就是ssl握手失败，先从代理服务器开始，内网的服务器几百年没有升过级，考虑能升级的就是openssl，squid，curl这三个东西。
因为操作系统版本太旧，原来的openssl还是0.98的，curl是7.14版本。先把openssl升级到了1.0.2，古董操作系统能支持的最新版本，使用curl访问依旧报错。接着把curl升级到了7.19.7，访问成功。

但是在应用中访问依旧报上述的错误，开始考虑ca证书的问题，同样是DigiCert的微信接口访问没有问题，也都是多域名证书。接着是jdk版本，古老的1.6，就顺便去看了下jdk支持的ssl协议，如下图：

![SSLContext Algorithms](https://v.dogl.xyz/imgblog/WX20190418-110916@2x.png)

jdk1.6缺少了tlsv1.2的支持，同时1.6下的jce安全机制存在bug。
可以选择更新jce的jar包，或者升级jdk版本。

最后，跟开头一样，把jdk从1.6升级到了1.8，访问正常，并不需要升级openssl。

