---
title: 使用 CoreDNS 作为数据中心内网 DNS 服务器
date: 2020/08/03
tags: CoreDNS
description: Some old articles may have been deprecated.
---

## 创建配置文件持久化目录，并复制配置文件

``` plain
mkdir -p /data/coredns/zones
cp hosts /data/coredns/
# 以添加 zone atompi.cc 为例：
mkdir -p /data/coredns/zones/atompi.cc
cp db.atompi.cc /data/coredns/zones/atompi.cc/
cp Corefile /data/coredns/
```
各配置文件示例：

- `Corefile`

``` plain
.:53 {
    forward . 223.5.5.5 223.6.6.6
    reload 10s
    loop
    log
    errors
    cache 30

    hosts /data/hosts {
        ttl 10
        reload 10s
        no_reverse
        fallthrough
    }
}

atompi.cc {
    forward . 223.5.5.5 223.6.6.6
    loop
    log
    errors
    cache 30

    auto {
        directory /data/zones/atompi.cc
        reload 10s
    }
}
```
- `hosts`

``` plain
192.168.1.2 www.test.com
```
- `db.atompi.cc`

``` plain
$TTL 3600
$ORIGIN atompi.cc.

@ IN SOA ns1.atompi.cc. atompi.atompi.cc. (
    2020072410 ; Serial
    1H         ; Refresh
    600        ; Retry
    7D         ; Expire
    600        ; Negative Cache TTL
)

@ IN NS ns1

ns1 IN A 192.168.1.2

; Custome
@                 IN A     192.168.1.2
*                 IN CNAME @
kube-apiserver    IN A     192.168.1.3
kube-dashboard    IN A     192.168.1.4
hub               IN A     192.168.1.5
```
- `docker-compose-host-network.yml`

``` plain
version: "3.3"

services:
  coredns:
    image: coredns/coredns:1.7.0
    command: -conf /data/Corefile
    volumes:
      - /data/coredns:/data:ro
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE
    read_only: true
    restart: on-failure
    network_mode: host
    logging:
      driver: "json-file"
      options:
        max-size: "100m"
        max-file: "10"
```
- `docker-compose.yml`

``` plain
version: "3.3"

services:
  coredns:
    image: coredns/coredns:1.7.0
    command: -conf /data/Corefile
    ports:
      - "53:53/udp"
      - "53:53/tcp"
      - "9153:9153/tcp"
    volumes:
      - /data/coredns:/data:ro
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE
    read_only: true
    restart: on-failure
    logging:
      driver: "json-file"
      options:
        max-size: "100m"
        max-file: "10"
```
## 使用 docker-compose 启动服务

``` plain
docker-compose up -d
# host network 模式，解决 CentOS 下 bridge 网络无法被同节点其他容器连接
docker-compose -f docker-compose-host-network.yml up -d
```
## PS1: 更新 RFC 1035-style zone 文件记录

对于 `hosts` 文件的更新会在 3 秒内更新解析值

对于 `auto` 插件指定的 RFC 1035-style zone 文件的更新，[官方文档](https://coredns.io/plugins/auto/#syntax)给出： `auto` 插件会在 `reload` 指定的时间重新读取 zone 文件，并且在 Serial 值更新是重新加载 zone 文件，只修改记录而不修改 Serial 值是无法应用更新的。

## PS2: zone 文件命名规则（必须遵从）

CoreDNS 根据“域”来分发解析请求到指定配置，所以必须匹配指定的域才能获取到正确的解析。 `auto` 插件在读取 `directory` 指定的 zone 目录时会根据正则表达式解析 zone 文件名（详见[官方说明](https://coredns.io/plugins/auto/#syntax)），默认规则为： `db\.(.*) {1}` i.e. 如果文件名为 `db.example.com` ， 那么解析到的域就是 `example.com` 。

同时 `Corefile` 文件中需要为每一个自定义域新建一组配置，如 `atompi.cc` 域：

``` plain
atompi.cc {
    forward . 223.5.5.5 223.6.6.6
    loop
    log
    errors
    cache 30

    auto {
        directory /data/zones/atompi.cc
        reload 10s
    }
}
```
