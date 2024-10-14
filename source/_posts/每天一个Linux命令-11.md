---
title: 每天一个 Linux 命令（11） -- file
date: 2018/01/11
tags: Linux
description: Some old articles may have been deprecated.
---

## 查看文件类型

``` plain
root@atompi:~# file 每天一个Linux命令-11.md
每天一个Linux命令-11.md: UTF-8 Unicode text

root@atompi:~# file atompi.io.tar.gz
atompi.io.tar.gz: gzip compressed data, last modified: Sun Jan  7 03:07:23 2018, from Unix
```
## 尝试查看压缩文件内部信息

``` plain
root@atompi:~# file -z atompi.io.tar.gz
atompi.io.tar.gz: POSIX tar archive (GNU) (gzip compressed data, last modified: Sun Jan  7 03:07:23 2018, from Unix)
```
对文件的类型存有疑问？对该文件运行 `file` 命令！
