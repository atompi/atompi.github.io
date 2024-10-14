---
title: 如何快速构建一个 Go 语言开发环境
date: 2019/11/30
tags: Go
description: Some old articles may have been deprecated.
---

## Overview

<li>本文适用于 Linux 环境；
</li>
<li>本文主体只对 Go 编译环境做一些简单的配置，如：环境变量、目录规划。
</li>
<li>本文末尾会附带一些有用的工具，对 Go 学习和开发都有很大帮助。
</li>

## 开始构建 Go 编译环境

- 安装 C/C++ 构建工具链（使用 CGO 特性）

``` plain
# Ubuntu
sudo apt install build-essential
# CentOS
sudo yum groupinstall "Development Tools"
```
- 下载 Go 二进制包

``` plain
cd ~/Downloads
GO_VERSION=1.13.4 &amp;&amp; wget https://dl.google.com/go/go$GO_VERSION.linux-amd64.tar.gz
```
- 解压 Go 二进制包并移动到 `/usr/local/go` 目录

``` plain
cd ~/Downloads
GO_VERSION=1.13.4 &amp;&amp; tar -zxf go$GO_VERSION.linux-amd64.tar.gz
sudo mv go /usr/local/
```
- 创建一些必要和不必要但建议创建并指定的目录

``` plain
sudo mkdir -p /data/goworkdir/{go,go-build}
sudo chown -R $USER:$USER /data/goworkdir
```
- 修改环境变量

只对当前用户生效：

对于 `bash` 用户，在 `$HOME/bashrc` 末尾新增以下内容；对于 `zsh` 用户，在 `$HOME/.zshrc` 末尾新增以下内容

对所有用户生效：

对于 Ubuntu 发行版， `bash` 用户在 `/etc/bash.bashrc` 末尾新增以下内容；`zsh` 用户，在 `/etc/zsh/zshrc` 末尾新增以下内容

对于 CentOS 发行版， `bash` 用户在 `/etc/profile` 末尾新增以下内容；`zsh` 用户，在 `/etc/zsh/zshrc` 末尾新增以下内容

``` plain
export GOROOT=/usr/local/go
export GOPATH=/data/goworkdir/go
export GOCACHE=/data/goworkdir/go-build
export PATH=$PATH:$GOROOT/bin
```
环境变量修改完成后退出当前用户重新登陆后生效，验证： `go env` 可输出 Go 环境信息

## 一些有用的工具

1. zeal —— 离线 API 文档神器

下载地址：

``` plain
https://zealdocs.org/
```
zeal 自带的 API 文档依赖于 [Dash](https://kapeli.com/dash) 项目，包含了众多的 API 文档和权威指南（包含 Go ），如果不够可以从下面的地址中获取更多社区维护的第三方 API 文档和 cheatsheets

``` plain
# cheatsheets
https://zeal-cheatsheets.herokuapp.com/
# Docsets
https://zealusercontributions.herokuapp.com/
```
如果还觉得不够（确实不够，没有 GoDoc ），这时候需要我们自己构建 Docset 导入了，这里只介绍 GoDoc 的构建，其他 Docset 的构建方式可以在[这里](https://kapeli.com/docsets)找到。

构建 GoDoc 使用 [godocdash](https://github.com/wuudjac/godocdash) 项目完成，原理大概是使用 `godoc` 工具在本地生成 GoDoc 然后通过暴露的 url 爬取内容并生成支持 Dash 格式的 docset 。

- 构建流程：

下载 Go 二进制包中不包含 godoc 工具，需要我们自行安装。

创建一个目录用来存放所有的 go 项目，如笔者使用 GoLand 开发，所有的 go 项目全部存放在 `/home/atompi/workdir/GolandProjects/` 目录下，下面我们把它简称为 `projectsDir`

在 `projectsDir` 下创建一个 `godoc` 文件夹（注意与项目名同名，但这不是源码文件）用来存放与该项目相关的所有文件（建议今后不管是个人创建的还是引用社区的项目都按这种格式统一管理）。

进入 `projectsDir/godoc` 目录下载 `godoc`

``` plain
export GOPATH=`pwd`
go get golang.org/x/tools/cmd/godoc
```
为 `godocd` 可执行文件创建软链接，以便在命令行直接使用 `godoc` 命令。

``` plain
# go get 命令会在 projectsDir/godoc 目录下创建 bin 目录存放编译好的可执行文件
sudo ln -s /home/atompi/workdir/GolandProjects/godoc/bin/godoc /usr/local/bin/
```
在 `projectsDir` 下创建一个 `godocdash` 文件夹

进入 `projectsDir/godocdash` 目录下载 `godocdash`

``` plain
export GOPATH=`pwd`
go get github.com/wuudjac/godocdash
```
为 `godocdash` 可执行文件创建软链接，以便在命令行直接使用 `godocdash` 命令。

``` plain
sudo ln -s /home/atompi/workdir/GolandProjects/godocdash/bin/godocdash /usr/local/bin/
```
创建 `projectsDir/godocmaker` 目录，用来存放待构建 GoDoc 的项目源码和构建出来的 Docset

``` plain
mkdir /home/atompi/workdir/GolandProjects/godocmaker
```
下载待构建 GoDoc 的项目源码，以 `github.com/prometheus/client_golang` 为例

``` plain
cd /home/atompi/workdir/GolandProjects/godocmaker
export GOPATH=`pwd`
# 只下载，不编译
go get -d github.com/prometheus/client_golang
```
构建 Docset ，在 `projectsDir/godocmaker` 目录下执行：

``` plain
godocdash
```
把构建出来的 Docset 移动到 zeal Docset 目录（ ~/.local/share/Zeal/Zeal/docsets/ ），重启 zeal 后就可以离线查看 GoDoc 了

``` plain
mv GoDoc.docset ~/.local/share/Zeal/Zeal/docsets/
```
1. tour —— Go 学习神器

[tour 中文版](https://tour.go-zh.org/) 是 Go 编程语言指南（ [A Tour of Go](https://tour.golang.org/) ）的中文社区维护版本，是学习 Go 语言（入门）最好的途径。 tour 工具可以开启一个 web 页面，以实例的方式介绍 Go 编程语言，用户可以在页面上直接编写简单的 Go 代码并实时执行获取执行结果。 tour 通过共有云的方式提供一个公共的入口，只要你能连接互联网就可以随时随地访问和使用，同时 tour 还提供了离线版本，以便在你无法连接互联网是同样可以通过 tour 工具学习和实践。

安装离线版 tour 非常简单只需要执行以下命令即可。

创建 `projectsDir/tour` 目录并在目录中执行：

``` plain
export GOPATH=`pwd`
# 安装中文版
go get github.com/Go-zh/tour
# 安装英文原版
go get golang.org/x/tour
```
建议为 `tour` 可执行文件创建软链接，以便在命令行直接使用 `tour` 命令。

命令行执行 `tour` 会自动打开默认浏览器并进入本地的 tour 主页
