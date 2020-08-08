---
layout: post
title:  "网络访问排查"
date:   2020-02-25 22:45:00
catalog:    true
tags:
    - dig
    - DNS
---

## 一、摘要
SocketTimeOutException Touble Shooting。

## 二、读者要求
本文需要有基本的网络知识，比如DNS、路由等，会使用控制台命令。

## 三、问题
通过域名访问服务器超时，根据网络连接的原理。
### 3.1 原理
1. DNS解析出IP地址；<br/>
DNS的解析出来的IP地址是错误的。
2. 建立与该IP地址的连接。<br/>
连接超时或者错误。
### 3.2 原因
1. DNS解析错误
2. 连接IP地址超时<br/>
2.1. 路由过长<br/>
2.2. 防火墙阻断<br/>

## 四、基准
### 4.1 正确的DNS解析结果。
该结果来自于dig命令。
```
$ dig tvmanager-scloud.cp21.ott.cibntv.net

; <<>> DiG 9.11.2-P1 <<>> tvmanager-scloud.cp21.ott.cibntv.net
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43974
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 3, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;tvmanager-scloud.cp21.ott.cibntv.net. IN A

;; ANSWER SECTION:
tvmanager-scloud.cp21.ott.cibntv.net. 195 IN CNAME tj3l-dlb.g1.cp21.ott.cibntv.net.
tj3l-dlb.g1.cp21.ott.cibntv.net. 79 IN  A       123.150.51.249
tj3l-dlb.g1.cp21.ott.cibntv.net. 79 IN  A       123.150.51.250

;; AUTHORITY SECTION:
cp21.ott.cibntv.net.    154     IN      NS      ns1.21cp.ott.cibntv.net.
cp21.ott.cibntv.net.    154     IN      NS      ns3.21cp.ott.cibntv.net.
cp21.ott.cibntv.net.    154     IN      NS      ns2.21cp.ott.cibntv.net.

;; ADDITIONAL SECTION:
ns2.21cp.ott.cibntv.net. 188    IN      A       123.59.177.211
ns3.21cp.ott.cibntv.net. 227    IN      A       101.236.15.219
ns1.21cp.ott.cibntv.net. 3      IN      A       101.236.15.219

;; Query time: 296 msec
;; SERVER: 10.72.240.10#53(10.72.240.10)
;; WHEN: 周二 2月 25 15:04:14 CST 2020
;; MSG SIZE  rcvd: 230
```
该域名解析有两个IP地址：++123.150.51.249++、++123.150.51.250++，该结果与该服务器的负责人确认。

## 五、排查顺序
### 5.1 目标设备
对于电视来说，需要adb connect上后，执行一些命令。
```
thomas@XancL03:~$ adb connect 10.58.104.110
* daemon not running; starting now at tcp:5037
* daemon started successfully
connected to 10.58.104.110:5555
```
#### 执行ping命令
```
thomas@XancL03:~$ adb shell
shell@mstarnapoli:/ $ ping -c 4 tvmanager-scloud.cp21.ott.cibntv.net
PING tj3l-dlb.g1.cp21.ott.cibntv.net (123.150.51.249) 56(84) bytes of data.
64 bytes from 123.150.51.249: icmp_seq=1 ttl=56 time=5.89 ms
64 bytes from 123.150.51.249: icmp_seq=2 ttl=56 time=7.35 ms
64 bytes from 123.150.51.249: icmp_seq=3 ttl=56 time=7.45 ms
64 bytes from 123.150.51.249: icmp_seq=4 ttl=56 time=26.9 ms

--- tj3l-dlb.g1.cp21.ott.cibntv.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 5.897/11.914/26.956/8.706 ms
```
第一行包括结果。
```
PING tj3l-dlb.g1.cp21.ott.cibntv.net (123.150.51.249) 56(84) bytes of data.
```
如果目标设备无法连接，可以考虑局域网PC来诊断。

### 5.2 局域网
Windows命令ping的解析结果。
```
C:\Users\thomas.XancL-NB\Desktop>ping tvmanager-scloud.cp21.ott.cibntv.net

正在 Ping tj3l-dlb.g1.cp21.ott.cibntv.net [123.150.51.249] 具有 32 字节的数据:
来自 123.150.51.249 的回复: 字节=32 时间=5ms TTL=56
来自 123.150.51.249 的回复: 字节=32 时间=3ms TTL=56
来自 123.150.51.249 的回复: 字节=32 时间=4ms TTL=56
来自 123.150.51.249 的回复: 字节=32 时间=4ms TTL=56

123.150.51.249 的 Ping 统计信息:
    数据包: 已发送 = 4，已接收 = 4，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 3ms，最长 = 5ms，平均 = 4ms
```
关键信息。tj3l-dlb.g1.cp21.ott.cibntv.net [123.150.51.249]，[]内为解析出的ip地址，属于正确的结果之一。
同时，与服务器直接的网络连接也是正常的。如果网络连接错误，如超时的话，可以排查一下到服务器的路由。

#### 路由
Windows OS上的tracert命令。
```
C:\Users\thomas.XancL-NB\Desktop>tracert tvmanager-scloud.cp21.ott.cibntv.net

通过最多 30 个跃点跟踪
到 tj3l-dlb.g1.cp21.ott.cibntv.net [123.150.51.249] 的路由:

  1    <1 毫秒   <1 毫秒    1 ms  10.58.100.1
  2     1 ms     1 ms     1 ms  10.58.254.134
  3     2 ms     1 ms     1 ms  123.125.37.227
  4    36 ms    28 ms    30 ms  123.125.26.249
  5    23 ms    19 ms    20 ms  172.17.43.37
  6     1 ms     2 ms     2 ms  172.17.49.25
  7    13 ms    21 ms     8 ms  172.17.49.18
  8     3 ms     2 ms     2 ms  172.17.49.17
  9     4 ms     4 ms     4 ms  172.17.50.6
 10    34 ms    28 ms    30 ms  172.17.50.30
 11     4 ms     3 ms     4 ms  123.150.51.249

跟踪完成。
```
看数据报停在哪个网关(路由设备)。
### 上游网络
如果已经出了局域网，请联系上游网络的管理员或者ISP来排查。