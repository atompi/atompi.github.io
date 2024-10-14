---
title: Docker Compose 安装 Jenkins
date: 2020/08/03
tags: Jenkins
description: Some old articles may have been deprecated.
---

## 安装 docker-compose

前往 `https://github.com/docker/compose/releases` 下载对应的 docker-compose 二进制可执行文件

将 docker-compose 移动至 `/usr/local/bin/` 目录下，并赋予可执行权限

## 镜像列表

``` plain
jenkins/jenkins:lts
nginx:1.16.1
docker:19.03.12-dind
```
## 创建挂载目录

为了持久化 CI 数据，我们需要将 jenkins 部分目录挂载到宿主机保存，如在 `/data` 目录下创建如下目录：

``` plain
mkdir -p /data/atompi-ci/jenkins
chown -R 1000:1000 /data/atompi-ci/jenkins
mkdir -p /data/atompi-ci/nginx/conf.d
mkdir -p /data/atompi-ci/nginx/logs
mkdir -p /data/atompi-ci/nginx/ssl
mkdir -p /data/atompi-ci/docker/data
mkdir -p /data/atompi-ci/docker/etc
```
**PS: 由于 jenkins images 在 使用 Dockerfile 构建时，默认 UID 为 1000 ，所以在挂载 jenkins home 目录时需要将宿主机目录 UID 设置为 1000 ，或者修改 docker-compose.yml 将 jenkins 容器用户修改为 root ，这里使用了修改 docker-compose.yml 的方式，出于安全考虑，建议修改宿主机目录 UID ，如： `chown -R 1000:1000 /data/atompi-ci/jenkins`**

## 使用 docker-compose 部署 jenkins

创建 `~/atompi-ci/` 目录并创建 `docker-compose.yml` 文件，文件内容如下：

``` plain
version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    environment:
      - JAVA_OPTS=-Duser.timezone=Asia/Shanghai
    volumes:
      - /data/atompi-ci/jenkins:/var/jenkins_home
    ports:
      - 8080:8080
  nginx:
    image: nginx:1.16.1
    ports:
      - 80:80
    volumes:
      - /data/atompi-ci/nginx/conf.d:/etc/nginx/conf.d
      - /data/atompi-ci/nginx/logs:/var/log/nginx
      - /data/atompi-ci/nginx/ssl:/etc/nginx/ssl
  docker:
    image: docker:19.03.12-dind
    privileged: true
    environment:
      - DOCKER_TLS_CERTDIR=
    volumes:
      - /data/atompi-ci/docker/data:/var/lib/docker
      - /data/atompi-ci/docker/etc:/etc/docker
```
创建 `/data/atompi-ci/nginx/conf.d/jenkins.conf` 文件，该文件为 Jenkins 的 Nginx 反向代理配置文件，如果不需要同时安装 Nginx 可以跳过，并注释掉 `docker-compose.yml` 中的 nginx 部分，文件内容如下：

``` plain
client_max_body_size 0;
upstream jenkins {
  keepalive 32; # keepalive connections
  server jenkins:8080; # jenkins ip and port
}
server {
    listen 80;
    server_name ci.atompi.cc;

    location / {
        proxy_pass http://jenkins;
        proxy_redirect $scheme://ci.atompi.cc $scheme://ci.atompi.com:8888;
        proxy_read_timeout 90;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root html;
    }
}
```
执行部署命令：

``` plain
docker-compose up -d
```
## 配置 jenkins

初始化 jenkins

浏览器打开我们在 nginx 配置文件中设置的域名，如: [http://ci.atompi.cc](http://ci.atompi.cc)

查看初始密码，在 jenkins 目录下 使用 docker-compose 命令查看 jenkins 日志，或者进入 jenkins 容器查看文件： `/var/jenkins_home/secrets/initialAdminPassword` （注意：初始化完成后，这个文件会消失）

``` plain
# 查看日志
# docker-compose logs jenkins
# 进入容器
# docker-compose exec jenkins bash
# cat /var/jenkins_home/secrets/initialAdminPassword
```
输入初始密码后进入插件安装界面，我们选择“安装推荐插件”。

去喝杯茶？

插件安装完成后，创建第一个管理员用户

选择保存并完成，之后进入确认 Jenkins URL 页面

初始化完成，开始使用 jenkins

进入 jenkins 首页

## 安装 Gitee Jenkins Plugin

1. 在线安装

管理员登录 jenkins ，进入： 系统管理 –&gt; 管理插件 –&gt; 可选插件，在插件搜索栏输入“ Gitee ”，即可在下方可选列表中勾选“ Gitee ”。选择“下载待重启后安装”即可在重启 Jenkins 后完成安装。

1. 上传插件 hpi 文件安装

从 [release](https://gitee.com/oschina/Gitee-Jenkins-Plugin/releases) 列表中进入最新发行版，下载对应的 `gitee-<version>.hpi</version>` 文件

进入：系统管理 –&gt; 管理插件 –&gt; 高级，在“上传插件”处上传 hpi 文件

- [Gitee Jenkins Plugin 使用文档](https://gitee.com/oschina/Gitee-Jenkins-Plugin#%E6%8F%92%E4%BB%B6%E9%85%8D%E7%BD%AE)
