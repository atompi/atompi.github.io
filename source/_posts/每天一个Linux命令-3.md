---
title: 每天一个 Linux 命令（3） -- mkdir
date: 2018/01/03
tags: Linux
description: Some old articles may have been deprecated.
---

## 创建空目录

``` plain
➜  Downloads mkdir testdir
➜  Downloads ll
total 540M
-rw-rw-r--  1 atompi atompi 508M Jan  1 20:38 Anaconda2-5.0.1-Linux-x86_64.sh
drwxr-xr-x 36 atompi atompi 4.0K Jan  5 05:59 mysql-5.6.38
-rw-rw-r--  1 atompi atompi  31M Sep 13 06:58 mysql-5.6.38.tar.gz
drwxrwxr-x  6 atompi atompi 4.0K Sep 21 07:20 redis-3.2.11
-rw-rw-r--  1 atompi atompi 1.5M Sep 21 07:20 redis-3.2.11.tar.gz
drwxrwxr-x  2 atompi atompi 4.0K Jan  6 04:18 testdir
-rw-rw-r--  1 atompi atompi    0 Jan  6 04:04 test.txt
➜  Downloads ll testdir
total 0
```
## 递归创建目录与子目录

``` plain
➜  ~ ll Downloads/testdir
total 0
➜  ~ mkdir -p Downloads/testdir/testdir1/testdir2
➜  ~ ll Downloads/testdir
total 4.0K
drwxrwxr-x 3 atompi atompi 4.0K Jan  6 04:19 testdir1
➜  ~ ll Downloads/testdir1
ls: cannot access 'Downloads/testdir1': No such file or directory
➜  ~ ll Downloads/testdir/testdir1
total 4.0K
drwxrwxr-x 2 atompi atompi 4.0K Jan  6 04:19 testdir2
```
