---
title: 每天一个 Linux 命令（12） -- cat
date: 2018/01/12
tags: Linux
description: Some old articles may have been deprecated.
---

## 在标准输出设备上查看文件内容

``` plain
root@atompi:~# cat 每天一个Linux命令-11.md
---
title: 每天一个 Linux 命令（11） -- file
date: 2018-01-11 21:31:34
tags:
  - Linux
  - shell
categories:
  - Linux
---

## 查看文件类型
```
## 同时查看多个文件（多个文件拼接输出）

``` plain
root@atompi:~# echo "test1" &gt; test1
root@atompi:~# echo "test2" &gt; test2
root@atompi:~# cat test1 test2
test1
test2
```
## 输出时显示行号

``` plain
root@atompi:~# cat test1 test2 -n
     1	test1
     2	test2
```
## 将多个文件拼接成一个新文件

``` plain
root@atompi:~# cat test1 test2 &gt; test3
root@atompi:~# cat test3
test1
test2
```
