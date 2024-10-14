---
title: Linux单网卡多IP配置
date: 2018/01/16
tags: Linux
description: Some old articles may have been deprecated.
---

## 临时生效

- 添加 IP 地址

``` plain
ifconfig eth0:0 192.168.1.1 netmask 255.255.255.0 up
# netmask 可以写，也可以不写，不写，则是使用标准的子网掩码，默认情况下的，在使用 VLSM 的时候，这个 netmask 就显得很重要了。
```
- 添加默认网关

``` plain
route add default gw 192.168.x.x
```
## 永久生效

ifconfig 配置的 IP 地址，只是临时有效，如果系统重启，则失效，而永久生效，要写到配置文件里面。

在 RedHat / CentOS 等系统，配置文件位于

`/etc/sysysconfig/network-scripts/ifcfg-xxx`

其中，ifcfg-xxx 为对应网卡的配置，如： ifcfg-eth0 使用 cat 查看一下安的内容

``` plain
cat /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=eth0
TYPE=Ethernet
UUID=a912766c-5500-44c4-85da-09a38f1121c0
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=dhcp
HWADDR=00:0C:29:22:75:2A
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=yes
IPV6INIT=no
NAME="System eth0"
```
`DEVICE` 代表的是设备名称，比如 eth0

`ONBOOT` 代表的是开机是否自动激活网卡，也就是说是否自动 up

`BOOTPROTO` 代表的是地址类型，有 DHCP 动态获取 (dhcp) ，也可以指定静态地址 (static)

`IPADDR` 代表的是静态地址的情况下，指定的 IP 地址是多少

`NETMASK` 子网掩码

`GATEWAY` 网关

`HWADDR` 网卡的 MAC 地址 (可以指定，也可以不指定)

这几个是常用的，没有特殊情况(比如桥接：`bonding`)，写这几个就可以了，以下是一个配置文件：

``` plain
cat /etc/sysconfig/network-scripts/ifcfg-eth0

DEVICE=eth0
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.7.7
NETMASK=255.255.255.0
GATEWAY=192.168.7.254
```
PS：配置文件里， 等号 (=) 前面的大写，后面的小写，若要使用 dhcp 动态获取 IP 地址，只需要将 `BOOTPROTO=dhcp` 再注释掉 `IPADDR` / `NETMASK` / `GATEWAY` 这三行，就可以了

另外，对于多网卡的配置，只需要复制配置文件，然后更改 `DEVICE=eth0:0` / `DEVICE=eth0:1` 再写上相关的IP地址，即可以了，多 IP 的情况下，不要写多个网关

而对于 Debian / Ubuntu 之类的 linux ，则网卡的配置文件要简单一些了，其配置文件位于：

`/etc/network/interfaces`

``` plain
auto lo
iface lo inet loopback

auto etho
inface eth0 inet static
      address 192.168.1.1
      netmask 255.255.255.0
      gateway 192.168.1.254

auto eth0:0
iface eth0:0 inet static
      192.168.10.189
      netmask 255.255.255.0

auto eth0:1
iface eth0:1 inet static
      address 192.168.12.12
      netmask 255.255.255.0
```
其中，`auto` 为自动启动网卡

iface 指定网卡设备，并指定类型，是动态还是静态，若要动态，则把 `static` 改为 `dhcp` 并注释掉 `address` / `netmask` / `gateway` 即可

至于 `address` 则是指定 IP 地址

`netmask` 指定子网掩码

`gateway` 指定网关
