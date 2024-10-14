---
title: 修改 docker 默认存储路径
date: 2018/02/05
tags: Docker
description: Some old articles may have been deprecated.
---

## 方法 1 、软链接

默认情况下 docker 的存放位置为： `/var/lib/docker`

可以通过下面命令查看具体位置：

``` plain
sudo docker info | grep "Docker Root Dir"
```
解决这个问题，最直接的方法当然是挂载分区到这个目录，但是我的数据盘还有其他东西，这肯定不好管理，所以采用修改镜像和容器的存放路径的方式达到目的。

这个方法里将通过软连接来实现。

首先停掉 docker 服务：

``` plain
sudo systemctl stop docker
# 或者
sudo service docker stop
```
然后移动整个  `/var/lib/docker` 目录到目的路径：

``` plain
sudo mv /var/lib/docker /root/data/docker
sudo ln -s /root/data/docker /var/lib/docker
```
这时候启动 docker 时发现存储目录依旧是 `/var/lib/docker` ，但是实际上是存储在数据盘的，你可以在数据盘上看到容量变化。

## 方法 2 、修改 docker 配置文件中镜像和容器的存放路径

指定镜像和容器存放路径的参数是 `--graph=/var/lib/docker` ，我们只需要修改配置文件指定启动参数即可。

docker 的配置文件可以设置大部分的后台进程参数，在各个操作系统中的存放位置不一致，在 Ubuntu 中的位置是： `/etc/default/docker` ，在 CentOS 中的位置是： `/etc/sysconfig/docker` 。

如果是 CentOS 7 则添加下面这行：

``` plain
OPTIONS='--graph=/root/data/docker --selinux-enabled -H fd://'
```
如果是 Ubuntu 则添加下面这行：

``` plain
DOCKER_OPTS="-g /root/data/docker -H fd://"
```
最后重新启动， Docker 的路径就改成 `/root/data/docker` 了。

centos 7 下，也可以修改 `docker.service` 文件，使用 `-g` 参数指定存储位置

``` plain
sudo vim /usr/lib/systemd/system/docker.service
```
``` plain
ExecStart=/usr/bin/dockerd --graph /root/data/docker
```
``` plain
# reload 配置文件
sudo systemctl daemon-reload

# 重启 docker
sudo systemctl restart docker
```
如果 docker 是 1.12 或以上的版本，可以修改（或新建）`daemon.json` 文件。

``` plain
vim /etc/docker/daemon.json
```
``` plain
{
  "registry-mirrors": ["http://7e61f7f9.m.daocloud.io"],
  "graph": "/root/data/docker"
}
```
``` plain
# 重启 docker
sudo systemctl restart docker
```
