---
title: 搭建 docker 源码编译环境
date: 2019/01/29
tags: Docker
description: Some old articles may have been deprecated.
---

## 0. 下载 moby/moby 源码

如果想要为 docker 贡献代码，则要进行以下步骤：

1. Fork and clone the Moby code： Fork 后 clone moby 源码
2. Set your signature and an upstream remote：设置签名和上游 remote

当你向 Docker 贡献代码时，你必须证明你同意（ [Developer Certificate of Origin](http://developercertificate.org/) ）开发人员原产地证书。你通过签署你的 git 提交来表明您的同意，如下所示：

``` plain
Signed-off-by: Pat Smith <pat.smith@email.com></pat.smith@email.com>
```
1. 具体做法：

``` plain
git config --local user.name "FirstName LastName"    # 必须是真实姓名
git config --local user.email "emailname@mycompany.com"    # 必须是有效可用的邮箱
git remote add upstream https://github.com/moby/moby.git
# 提交代码时使用 git commit -s 自动签署
git commit -s -m "Making a dry run test."
```
## 1. 净化本地 Docker 环境 Remove images and containers

**这个步骤并非必要的，但是是推荐的： Moby developers run the latest stable release of the Docker software. They clean their local hosts of unnecessary Docker artifacts such as stopped containers or unused images. Cleaning unnecessary artifacts isn’t strictly necessary, but it is good practice, so it is included here.**

``` plain
docker system prune -a
```
## 2. 启动开发环境容器 Start a development container

进入自己 fork 的 moby 项目 clone 目录，切换到某分支（如： `git checkout dry-run-test` ）

``` plain
make BIND_DIR=. shell    # 该命令会构建一个名为docker-dev:dry-run-test 的镜像
```
执行结果：（容器运行命令会打印在终端并运行，之后要执行复制保存即可）

``` plain
Successfully built 87f31121696f
Successfully tagged docker-dev:overlay-vxlan-port
docker run --rm -i --privileged  -e DOCKER_CROSSPLATFORMS -e BUILD_APT_MIRROR -e BUILDFLAGS -e KEEPBUNDLE -e DOCKER_BUILD_ARGS -e DOCKER_BUILD_GOGC -e DOCKER_BUILD_OPTS -e DOCKER_BUILD_PKGS -e DOCKER_BUILDKIT -e DOCKER_BASH_COMPLETION_PATH -e DOCKER_CLI_PATH -e DOCKER_DEBUG -e DOCKER_EXPERIMENTAL -e DOCKER_GITCOMMIT -e DOCKER_GRAPHDRIVER -e DOCKER_LDFLAGS -e DOCKER_PORT -e DOCKER_REMAP_ROOT -e DOCKER_STORAGE_OPTS -eDOCKER_USERLANDPROXY -e DOCKERD_ARGS -e TEST_INTEGRATION_DIR -e TESTDIRS -e TESTFLAGS -e TIMEOUT -e HTTP_PROXY -e HTTPS_PROXY -e NO_PROXY -e http_proxy -e https_proxy -e no_proxy -e VERSION -e PLATFORM -e DEFAULT_PRODUCT_LICENSE -e PRODUCT -v "/home/atompi/GolandProjects/src/github.com/mission802/moby/.:/go/src/github.com/docker/docker/." -v "/home/atompi/GolandProjects/src/github.com/mission802/moby/.git:/go/src/github.com/docker/docker/.git" -v docker-dev-cache:/root/.cache    -t "docker-dev:overlay-vxlan-port" bash
```
启动容器：

``` plain
docker run --rm -i --privileged  -e DOCKER_CROSSPLATFORMS -e BUILD_APT_MIRROR -e BUILDFLAGS -e KEEPBUNDLE -e DOCKER_BUILD_ARGS -e DOCKER_BUILD_GOGC -e DOCKER_BUILD_OPTS -e DOCKER_BUILD_PKGS -e DOCKER_BUILDKIT -e DOCKER_BASH_COMPLETION_PATH -e DOCKER_CLI_PATH -e DOCKER_DEBUG -e DOCKER_EXPERIMENTAL -e DOCKER_GITCOMMIT -e DOCKER_GRAPHDRIVER -e DOCKER_LDFLAGS -e DOCKER_PORT -e DOCKER_REMAP_ROOT -e DOCKER_STORAGE_OPTS -eDOCKER_USERLANDPROXY -e DOCKERD_ARGS -e TEST_INTEGRATION_DIR -e TESTDIRS -e TESTFLAGS -e TIMEOUT -e HTTP_PROXY -e HTTPS_PROXY -e NO_PROXY -e http_proxy -e https_proxy -e no_proxy -e VERSION -e PLATFORM -e DEFAULT_PRODUCT_LICENSE -e PRODUCT -v "/home/atompi/GolandProjects/src/github.com/mission802/moby/.:/go/src/github.com/docker/docker/." -v "/home/atompi/GolandProjects/src/github.com/mission802/moby/.git:/go/src/github.com/docker/docker/.git" -v docker-dev-cache:/root/.cache    -t "docker-dev:overlay-vxlan-port" bash
```
构建 `dockerd` 二进制文件

``` plain
hack/make.sh binary
```
运行 `make install` ，将二进制文件复制到容器的 `/usr/local/bin/` 目录

``` plain
make install
```
启动在后台运行的 Engine 守护程序

``` plain
dockerd -D &amp;
```
