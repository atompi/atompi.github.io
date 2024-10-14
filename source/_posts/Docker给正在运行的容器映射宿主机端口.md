---
title: Docker给正在运行的容器映射宿主机端口
date: 2018/01/18
tags: Docker
description: Some old articles may have been deprecated.
---

## 方式 1 ：iptables 转发

### 获得容器 IP

``` plain
docker inspect 'container_name' | grep "\"IPAddress\": \""|head -1 | cut -d "\"" -f 4
```
### iptables 转发端口

将容器的 `8000` 端口映射到 docker 主机的 `8001` 端口

``` plain
iptables -t nat -A  DOCKER -p tcp --dport 8001 -j DNAT --to-destination 172.17.0.2:8000
```
### 一步搞定

``` plain
iptables -t nat -A  DOCKER -p tcp --dport 8001 -j DNAT --to-destination $(docker inspect 'container_name' | grep "\"IPAddress\": \""|head -1 | cut -d "\"" -f 4):8000
```
## 方式 2 ： 重新 build 镜像， EXPOSE 端口，重新运行容器

新建一个空目录，编写 Dockerfile

``` plain
mkdir image_name_version2
cd image_name_version2
vim Dockerfile
```
Dockerfile 内容如下：

``` plain
FROM image_name:version

EXPOSE 8000
```
根据新的 Dockerfile 构建镜像

``` plain
docker build -t image_name:version2 .
```
基于新的镜像运行新的容器

``` plain
docker run -p 8001:8000 ...
```
