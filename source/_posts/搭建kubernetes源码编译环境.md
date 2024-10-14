---
title: 搭建 Kubernetes 源码编译环境
date: 2019/10/26
tags: Kubernetes
description: Some old articles may have been deprecated.
---

## 0. Overview

虽然可以使用本地 golang 安装来构建 Kubernetes，但在 Docker 容器中运行的构建过程简化了初始设置，并提供了非常一致的构建和测试环境。尽管如此，本文还是会介绍本地 golang 和 Docker 容器两种构建方式，以供读者自由选择。

## 1. 本地 golang 构建（适用于对 Kubernetes 有二次开发/源码阅读需求的用户）

### 1.1 安装依赖

- 安装 Golang

见[如何快速构建一个 Go 语言开发环境](https://atompi.io/2019/11/30/%E5%A6%82%E4%BD%95%E5%BF%AB%E9%80%9F%E6%9E%84%E5%BB%BA%E4%B8%80%E4%B8%AAGo%E8%AF%AD%E8%A8%80%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83/)

### 1.2 下载 Kubernetes 源码

[项目地址](https://github.com/kubernetes/kubernetes)

项目默认分支为 `master` 可以使用以下命令下载指定分支 / tag ：

``` plain
# 创建并进入 Kubernetes 专用的 workdir ，如：
mkdir -p /home/atompi/workdir/GolandProjects/Kubernetes
cd /home/atompi/workdir/GolandProjects/Kubernetes
export GOPATH=`pwd`
mkdir -p $GOPATH/src/k8s.io
cd $GOPATH/src/k8s.io
git clone https://github.com/kubernetes/kubernetes -b <branch name="" tag=""></branch>
```
### 1.3 编译

`KUBE_BUILD_PLATFORMS` 指定目标平台， `WHAT` 指定编译的组件，通过 `GOFLAGS` 和 `GOGCFLAGS` 传入编译时参数，如此处编译 `kubelet` 组件。

``` plain
KUBE_BUILD_PLATFORMS=linux/amd64 make all WHAT=cmd/kubelet GOFLAGS=-v GOGCFLAGS="-N -l"
```
make 参数说明：

- `WHAT` ：指定需要编译的包如 `WHAT=cmd/kubelet` ，如果不指定则编译全部
- `all` ：在本地环境中进行编译，相对于 `release` 、 `quick-release` 是在容器中完成编译并打包成 Docker 镜像，默认为 `all`
- `clean` :清理编译结果以便重新编译

### 快速编译

编译过程较长，编译后的文件在 `_output` 里。可以在 `cmd/xxx` 目录下执行 `go build -v` 构建当前平台下当前的二进制包，构建结果直接输出在当前文件夹。如快速构建 kubelet ：

``` plain
cd cmd/kubelet
go build -v
```
## 2. 在 Docker 容器中运行构建

### 2.1 环境要求

只需要在本地安装 Docker daemon 即可

### 2.2 关键性脚本（构建命令及说明）

以下脚本都可以在 `build/` 目录中找到。注意：所有脚本必须在 Kubernetes 根目录（即 `$GOPATH/src/k8s.io/kubernetes` ）运行。

<li>`build/run.sh` ：在用来构建的 docker 容器中运行命令。常用的有以下调用参数：
<ul>
- `build/run.sh make`：运行构建，默认仅构建 linux 所有二进制文件。可以根据需要传递 make 参数来跨平台构建或者构建指定包，如下：
- `build/run.sh make cross` ：构建所有平台的所有二进制文件
- `build/run.sh make kubectl KUBE_BUILD_PLATFORMS=darwin/amd64` ：为特定平台构建特定二进制文件（如这行命令表示只构建 `darwin/amd64` 平台的 `kubelet` 二进制文件）
- `build/run.sh make test` ：运行所有单元测试
- `build/run.sh make test-integration` ：运行集成测试
- `build/run.sh make test-cmd` ：运行 CLI 测试

`build/copy-output.sh` ：将构建结果从 Docker 容器的 `_output/dockerized/bin` 文件夹复制到本地 `_output/dockerized/bin` 文件夹同时复制在构建过程中生成的特定文件属性。该命令已经在 `build/run.sh` 中自动运行，也可在完成构建后单独多次运行。

`build/make-clean.sh` ：清除本地 `_output` 的内容，删除所有本地构建的容器镜像，删除 Docker 持久化数据卷，即清空构建过程中产生的所有文件。

`build/shell.sh` ：进入挂载有当前代码的构建容器中，相当于 `docker exec -it <container> bash</container>`

### 2.3 基本流程

<li>位于 `build/` 目录下（父目录下，不包含子目录下）的脚本用于构建和测试。这些脚本用于构建 `kube-build` Docker 镜像（基于 `build/build-image/Dockerfile` 构建），然后在基于该镜像启动的容器中执行构建命令。这些脚本同时确保每次运行都缓存正确的数据以进行增量构建，并将结果复制回容器。
</li>
<li>通过首先在 `_output/images/build-image` 中创建一个 `context` 目录来构建 `kube-build` 容器镜像。在 `context` 而不是在 `Kubernetes` 存储库的根目录进行镜像构建，可以以最大程度地减少构建镜像时需要打包的数据量。
</li>
<li>从此镜像运行 3 个不同的容器实例。第一个是持久化数据卷（ `data` ）容器，用于存储需要持久存储以支持增量构建的所有数据。接下来是一个同步（ `rsync` ）容器，用于将数据传入和传出到持久化数据卷容器。最后一个是构建（ `build` ）容器，用于实际执行构建操作。每次运行后，持久化数据卷容器在运行期间都将保留，而同步和构建容器将被删除。
</li>
<li>`rsync` 在后台透明地使用，以有效地将数据移入和移出容器。这将使用 Docker 选择的临时端口。您可以通过设置 `KUBE_RSYNC_PORT` 变量来修改它。
</li>
<li>所有 Docker 名称都以从文件路径派生的哈希作为后缀（以允许在 CI 机器上同时使用）和版本号。当版本号更改时，所有状态都将清除，并开始全新构建。这允许更改构建基础结构，并向 CI 系统发出需要删除旧工件的信号。
</li>

### 2.4 代理设置

如果你需要通过代理上网，并且让这些脚本使用 docker-machine 在 macOS 上为你设置本地 VM ，则需要配置 Kubernetes 构建的代理设置，具体操作为定义以下环境变量：

``` plain
export KUBERNETES_HTTP_PROXY=http://username:password@proxyaddr:proxyport
export KUBERNETES_HTTPS_PROXY=https://username:password@proxyaddr:proxyport
```
同时可以选择为 Kubernetes 构建指定不需要代理的地址，例如：

``` plain
export KUBERNETES_NO_PROXY=127.0.0.1
```
如果使用 sudo 进行 Kubernetes 的构建（例如 `make quick-release` ），则需要运行 `sudo -E make quick-release` 来传递环境变量。

### 2.5 使用远程 Docker engine 构建（不推荐）

原理同使用本地 Docker engine 构建，只是需要通过配置环境变量将远程的 Docker engine 当作本地使用（需要创建 SSH Tunnel 将本地 rsync 端口映射到远程以从远程将构建结果复制回本地）

详细操作这里不作介绍，可参考[官方配置](https://github.com/kubernetes/kubernetes/tree/master/build#really-remote-docker-engine)

### 2.6 构建发行版

`build/release.sh` 脚本将构建发行版。它将构建二进制文件、运行测试同时构建 Docker 映像（可选）。

构建结果是 tar 文件：主要是 `kubernetes.tar.gz` ，这个 tar 文件中包括：

- 交叉编译的客户端。
- kubectl ，用于基于平台选择和运行正确的客户端二进制文件。
- Examples
- 各种云的集群部署脚本
- 包含所有服务端二进制文件的 tar 文件

此外，还创建了其他一些 tar 文件：

- `kubernetes-client-*.tar.gz` 特定平台的客户端二进制文件
- `kubernetes-server-*.tar.gz` 特定平台的服务端二进制文件

构建最终发行版 tar 时，tar 包里面的文件会暂存在 `_output/release-stage` ，然后再将其打入 tar 包到 `_output/release-tars` 目录下。

### 2.7 可重复性的构建环境（ Reproducible Builds 项目）

可重复性的构建是一组软件开发实践，可创建从源代码到二进制代码的独立可验证路径。见 [Reproducible builds](https://reproducible-builds.org/docs/definition/)

`make release` 、其派生的 `make quick-release` 以及 [Bazel 构建工具](https://www.bazel.build/) 都提供了一个封闭的构建环境，为构建提供一定程度的可重复性（ `make` 本身是不封闭的）。

Kubernetes 构建环境支持 Reproducible Builds 项目指定的 [`SOURCE_DATE_EPOCH` 环境变量](https://reproducible-builds.org/specs/source-date-epoch/)，可以将其设置为 UNIX 时间戳。这将用于嵌入已编译的 Go 二进制文件中的构建时间戳，也许未来还会用于 Docker 映像。这个变量的一个合理设置是使用从正在构建的树的顶端开始的提交时间戳（这是Kubernetes CI 系统使用的）。例如，可以使用以下设置：

``` plain
SOURCE_DATE_EPOCH=$(git show -s --format=format:%ct HEAD)
```
