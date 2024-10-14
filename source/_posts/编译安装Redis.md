---
title: Linux 下编译安装 Redis 3.x
date: 2018/01/08
tags: Linux
description: Some old articles may have been deprecated.
---

## 下载源码包

下载地址：[http://download.redis.io/releases/](http://download.redis.io/releases/)

``` plain
wget http://download.redis.io/releases/redis-3.2.11.tar.gz
```
## 解压源码包

``` plain
tar -zxf redis-3.2.11.tar.gz
```
## 编译

``` plain
cd redis-3.2.11
make
```
## 安装

``` plain
sudo make install
```
## 初始化，创建 redis server

``` plain
cd utils
sudo ./install_server.sh
# 此处可根据需要进行基础配置，亦可一路回车（ Enter ）完成初始化，然后修改 `/etc/redis/6379.conf` 文件，完成配置
```
## 修改一些必要的配置（开发环境）

``` plain
vim /etc/redis/6379.conf
```
禁用保护模式、允许远程连接（可选）

``` plain
protected-mode no
#bind 127.0.0.1
```
## 关于 3.2 新特性： protected-mode

3.2 后新增 protected-mode 配置，默认是 yes，即开启保护模式。官方说明如下：

``` plain
# Protected mode is a layer of security protection, in order to avoid that
# Redis instances left open on the internet are accessed and exploited.
#
# When protected mode is on and if:
#
# 1) The server is not binding explicitly to a set of addresses using the
#    "bind" directive.
# 2) No password is configured.
#
# The server only accepts connections from clients connecting from the
# IPv4 and IPv6 loopback addresses 127.0.0.1 and ::1, and from Unix domain
# sockets.
#
# By default protected mode is enabled. You should disable it only if
# you are sure you want clients from other hosts to connect to Redis
# even if no authentication is configured, nor a specific set of interfaces
# are explicitly listed using the "bind" directive.
protected-mode yes
```
开启后，若在服务端没有配置 `bind ip` （注释掉 `bind` 字段的配置） 或者没有设置 `masterauth <master-password></master-password>` （ server 端连接密码）， client 连接 server 时就出现如下报错信息：

``` plain
192.168.19.128:6379&gt; keys *
(error) DENIED Redis is running in protected mode because protected mode is enabled, no bind address was specified, no authentication password is requested to clients. In this mode connections are only accepted from the loopback interface. If you want to connect from external computers to Redis you may adopt one of the following solutions: 1) Just disable protected mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to make this change permanent. 2) Alternatively you can just disable the protected mode by editing the Redis configuration file, and settingthe protected mode option to 'no', and then restarting the server. 3) If you started the server manually just for testing, restart it with the '--protected-mode no' option. 4) Setup a bindaddress or an authentication password. NOTE: You only need to do one of the above things in order for the server to start accepting connections from the outside.
```
强烈建议在生产环境开启 `protected-mode` 以提高安全性，对于开发环境建议使用 “禁用保护模式、允许远程连接（可选）” 的配置。
