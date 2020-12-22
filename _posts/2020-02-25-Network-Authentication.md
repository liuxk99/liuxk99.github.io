---
layout: post
title:  "使用curl进行上网认证"
date:   2020-02-12 10:41:00
catalog:    true
tags:
    - Authentication
    - HTTP
    - curl
    - tools
    - 开胃小菜
---

## 一、摘要
使用curl来进行上网认证。

## 二、读者要求
本文需要有基本的网络知识，HTTP协议基本知识，比如session, cookie等，会使用控制台命令。

## 三、缘起
由于疫情的关系，不能到办公室上网，但是开发工作又不能停，好在大家都有VPN，但是通过ssh登录办公室的电脑后，只有终端命令行界面，这样上网认证就遇到了麻烦。
### 3.1 上网认证系统
原先在办公室电脑都是通过浏览器来进行上网认证的，通常网关会设置预期时间，最近是24小时。差不多第二天打开电脑访问网站就会弹出
![上网认证系统页面](/images/Identity-Authentication-System.png)。
需要使用公司内网的账号密码来认证，在页面输入账号及密码，验证通过设备才能够上网。
### 3.2 思路
不在电脑旁的情况就麻烦了，常见的办法包括使用远程桌面登录或者终端界面来完成认证。
方案1需要安装软件，而apt-get安装软件也需要访问互联网，那就死结了。方案2需要一些网络命令，如：curl, wget等。看了一下有curl，有搞头。
```
$ uname -a
Linux XancL03 4.4.0-148-generic #174~14.04.1-Ubuntu SMP Thu May 9 08:17:37 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```
curl
```
$ curl --version
curl 7.35.0 (x86_64-pc-linux-gnu) libcurl/7.35.0 OpenSSL/1.0.1f zlib/1.2.8 libidn/1.28 librtmp/2.3
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtmp rtsp smtp smtps telnet tftp 
Features: AsynchDNS GSS-Negotiate IDN IPv6 Largefile NTLM NTLM_WB SSL libz TLS-SRP
```
curl的使用相对容易，从网页的认证过程来看，账号密码是必须的，原则上可行！
### 待续...