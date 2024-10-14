---
title: 离线安装 Harbor v2
date: 2021/02/23
tags:
  - Harbor
  - Docker
  - Cloud Native
description: Some old articles may have been deprecated.
---

## 什么是 harbor ？

Harbor 是一个开源、可信、云原生的镜像仓库项目，用于存储，签名和扫描内容。

Harbor 通过添加用户通常需要的功能（如安全性，身份验证和管理）来扩展开源 Docker 镜像分发工具（如 Docker registry）。

Harbor 使 registry 更接近构建和运行环境，并且可以提高镜像传输效率。

Harbor 支持在 registry 之间复制映像，还提供高级安全功能，如用户管理，访问控制和活动审计。

## 特征

- 基于角色的访问控制：用户和存储库通过“项目”进行组织，用户可以对项目下的镜像赋予不同的权限。
- 基于策略的镜像复制：可以在多个 registry 实例之间复制（同步）映像，并自动重试错误。非常适合负载平衡，高可用性，多数据中心，混合和多云场景。
- 漏洞扫描：Harbor 定期扫描镜像并警告用户漏洞。
- LDAP / AD 支持：Harbor 与现有企业 LDAP / AD 集成，用于用户身份验证和管理。
- 镜像删除和垃圾收集：可以删除镜像，并可以回收它们的空间。
- 公证：可以确保图像的真实性。
- 图形化界面：用户可以轻松浏览，搜索存储库和管理项目。
- 审计：跟踪存储库的所有操作。
- RESTful API：适用于大多数管理操作的 RESTful API，易于与外部系统集成。
- 易于部署：提供在线和离线安装程序。
- 支持 Helm 包管理

## 部署 & 运行

### 环境要求：

目前只支持在 Linux 操作系统下部署， 安装最新版本的 docker 及 docker-compose，最低版本要求如下：

```
Docker engine: Version 17.06.0-ce+ or higher
Docker Compose: Version 1.18.0 or higher
```

同时需要开放如下端口：

```
80: HTTP
443: HTTPS
```

## 开始部署

- 下载离线安装程序

下载地址：`https://github.com/vmware/harbor/releases`

选择：`harbor-offline-installer-.tgz`

- 解压安装包

```
tar -xf harbor-offline-installer-.tgz
```

- 配置 Harbor

配置文件模板为：`harbor.yml.tmpl`

复制模板文件，并命名为 `harbor.yml`

在 `harbor.yml` 中有两类参数：必需参数和可选参数。

- 必选参数：需要在配置文件中设置这些参数。如果用户在 `harbor.yml` 中更新它们并运行 `install.sh` 脚本以重新安装 Harbor，它们将生效。
- 可选参数：这些参数对于更新是可选的，即用户可以将它们保留为默认值，并在启动 Harbour 后在 Web UI 上更新它们。如果它们设置在 `harbor.yml` 中，它们只会在第一次启动 Harbour 时生效。后续在 Web UI 中的更新将忽略 `harbor.yml` 中这些参数。

在这里，我只列出部分必选参数的配置，具体参数说明见文章末尾处给出的官方安装文档连接

1. 必选参数：

- `hostname`: 访问 harbor 的域名，可以使用主机 IP，但建议使用域名

```
hostname = reg.atompi.com
```

- `http / https`: 访问 harbor 的 url 协议，若只使用 `http`，则注释掉 `https` 项即可（如下），若启用 `https` 请参考 [Configuring Harbor with HTTPS Access](https://goharbor.io/docs/2.0.0/install-config/configure-https/)

```
# http related config
http:
  # port for http, default is 80. If https enabled, this port will redirect to https port
  port: 80

# https related config
#https:
  # https port for harbor, default is 443
  #port: 443
  # The path of cert and key files for nginx
  #certificate: /your/certificate/path
  #private_key: /your/private/key/path
```

- `harbor_admin_password`: 超级管理员用户 `admin` 的登录密码
- `database.password`: 数据库 root 用户密码，用于指定 Harbor 默认数据库 root 用户密码，生产环境建议修改这个值为复杂密码

```
database:
  # The password for the root user of Harbor DB. Change this before any production use.
  password: root123
```

- `data_volume`: 由于 Harbor 所有组件都使用 docker 容器化部署，所以需要将容器数据持久化到宿主机目录，该配置项就是指定持久化目录的挂载点，建议在某目录下创建 `harbor` 文件夹，并作为挂载点，便于统一管理和迁移。

```
data_volume: /data/harbor
```

## 执行 install.sh 开始安装

默认情况下，不带任何参数的 `install.sh` 脚本只安装 Harbor 主服务，如果需要安装其他附加服务，可带指定参数安装，可安装的附加服务有：

```
Harbor with Notary （ --with-notary ）：一个允许任何人信任任意数据集合的项目，用于运行和与可信集合交互。
Harbor with Clair （--with-clair）：Docker 镜像安全漏洞扫描工具
Harbor with Chart Repository Service（--with-chartmuseum）：Helm 包管理工具
```

这里我们安装带 Helm 包管理的 Harbor

```
sudo ./install.sh --with-chartmuseum
```

## 登录 harbor 并配置 Docker “insecure-registries”

- Web UI 地址： `http://reg.atompi.com`

- docker login

修改 docker daemon 配置文件 `/etc/docker/daemon.json`，添加如下配置项：

```
{
    "insecure-registries": ["reg.atompi.com"]
}
```

docker 登录 registry

```
docker login reg.atompi.com
```

## 扩展阅读：

- [官方安装文档](https://goharbor.io/docs/2.0.0/install-config/)
- [官方管理文档](https://goharbor.io/docs/2.0.0/administration/)
