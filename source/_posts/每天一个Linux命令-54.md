---
title: 每天一个 Linux 命令（54） -- ping
date: 2018/02/23
tags: Linux
description: Some old articles may have been deprecated.
---

测试主机之间网络的连通性

## 补充说明

**ping命令** 用来测试主机之间网络的连通性。执行ping指令会使用ICMP传输协议，发出要求回应的信息，若远端主机的网络功能没有问题，就会回应该信息，因而得知该主机运作正常。

## 语法

``` plain
ping (选项) (参数)
```
## 选项

``` plain
-d：使用Socket的SO_DEBUG功能；
-c&lt;完成次数&gt;：设置完成要求回应的次数；
-f：极限检测；
-i&lt;间隔秒数&gt;：指定收发信息的间隔时间；
-I&lt;网络界面&gt;：使用指定的网络界面送出数据包；
-l&lt;前置载入&gt;：设置在送出要求信息之前，先行发出的数据包；
-n：只输出数值；
-p&lt;范本样式&gt;：设置填满数据包的范本样式；
-q：不显示指令执行过程，开头和结尾的相关信息除外；
-r：忽略普通的Routing Table，直接将数据包送到远端主机上；
-R：记录路由过程；
-s&lt;数据包大小&gt;：设置数据包的大小；
-t&lt;存活数值&gt;：设置存活数值TTL的大小；
-v：详细显示指令的执行过程。
```
## 参数

目的主机：指定发送ICMP报文的目的主机。

## 实例

``` plain
[root@AY1307311912260196fcZ ~]# ping www.jsdig.com
PING host.1.jsdig.com (100.42.212.8) 56(84) bytes of data.
64 bytes from 100-42-212-8.static.webnx.com (100.42.212.8): icmp_seq=1 ttl=50 time=177 ms
64 bytes from 100-42-212-8.static.webnx.com (100.42.212.8): icmp_seq=2 ttl=50 time=178 ms
64 bytes from 100-42-212-8.static.webnx.com (100.42.212.8): icmp_seq=3 ttl=50 time=174 ms
64 bytes from 100-42-212-8.static.webnx.com (100.42.212.8): icmp_seq=4 ttl=50 time=177 ms
...按Ctrl+C结束

--- host.1.jsdig.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2998ms
rtt min/avg/max/mdev = 174.068/176.916/178.182/1.683 ms
```
