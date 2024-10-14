---
title: 如何向使用 Go 开发的开源项目贡献代码
date: 2019/12/01
tags: Go
description: Some old articles may have been deprecated.
---

## 0. Overview

当我们要向一个开源项目贡献代码时通常的步骤是：

<li>fork 原项目到自己的账号上
</li>
<li>在本地将 fork 的项目下载下来，开发，编译测试
</li>
<li>本地检查没问题之后 push 到自己 fork 的仓库
</li>
<li>按原项目贡献代码规范检查自己的代码以符合规范（一些热门的/专业社区维护的项目一般都有一套贡献规范，如 Kubernetes 、 moby （原 Docker ） 、 Go）
</li>
<li>一切安排妥当后向原项目提交 PR 等待合并到原项目分支
</li>

整个流程看起来十分简单，但这一套用在 Go 项目仿佛有点行不通了。

## 1. 遇到的问题

我们在编写 Go 代码的时候通常会引用本项目或在其他项目的包，通常我们会使用远程地址指定：

``` plain
import (
    gitee.com/oscstudio/gitee-exporter/config
)
```
这时如果我们发现依赖的代码包中存在缺陷，或者我们有一个很棒的想法想要合并到源项目中，因此我们需要修改源项目的代码。于是我们按照上面的流程走一遍。

这时候问题来了：如果我们 fork 了源项目，然后通过 `go get` 拉取我们 fork 的代码时，会收到如下错误输出

``` plain
(base) ➜  blackbox_exporter proxy go get github.com/atompi/blackbox_exporter
# github.com/atompi/blackbox_exporter
src/github.com/atompi/blackbox_exporter/main.go:119:19: cannot use registry (type *"github.com/atompi/blackbox_exporter/vendor/github.com/prometheus/client_golang/prometheus".Registry) as type *"github.com/prometheus/blackbox_exporter/vendor/github.com/prometheus/client_golang/prometheus".Registry in argument to prober
(base) ➜  blackbox_exporter
```
如果此时我们修改代码中引用这个包的部分的代码，我们会修改到一些不必要（甚至不允许）修改的代码，导致我们的代码即使编译通过也无法合并到源项目。

还有一个问题，即 [Dependency hell](https://en.wikipedia.org/wiki/Dependency_hell) （依赖地狱）。依赖地狱是一个通俗的术语，用于体现使用了某些安装了依赖于其他特定版本的软件包的软件用户的沮丧心情。

Github 上不乏有这样的评论： `"When I run [the tests] they always pass, but that is because they import the package from [the package path] and not my local fork."` 。即：当我运行或者测试时，它们始终会通过，但这只是因为它们是从包源路径而非从我本地 fork 的包导入的。

## 2. 解决办法

为了避免上面遇到的问题，我们需要换一种方式来修改“ fork ”的代码

首先，我们在 Gitee 或者 Github 上 fork 我们需要贡献代码的项目，然后我们使用 `go get` 拉取源项目而非我们 fork 的项目，这样我们的代码 import 部分都是正确且不需要做不必要的修改的。

然后， `go get` 会将项目的 git 仓库拉到本地的 `$GOPATH/src` 目录下， `git remote -v` 可以看到，远程地址为源项目的地址，此时我们可以通过 `git remote add fork xxxx` 命令添加我们 fork 的仓库地址，让当前 git 仓库拥有两个远程地址。

最后，我们修改并测试完成之后可以将修改后的代码推送到我们 fork 的远程地址，这样就可以安全的向源项目提交 PR 了。

以上为笔者在实践中总结的一种向使用 Go 开发的开源项目贡献代码的解决方案，该方案改动少，对代码没有入侵性，操作简单。如果读者有更好的方式，可以在评论中留言，大家一起探讨。
