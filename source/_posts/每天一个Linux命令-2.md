---
title: 每天一个 Linux 命令（2） -- touch
date: 2018/01/02
tags: Linux
description: Some old articles may have been deprecated.
---

## 将文件改为当前时间

``` plain
➜  ~ date
Sat Jan  2 20:38:43 PST 2018
➜  ~ ll Anaconda2-5.0.1-Linux-x86_64.sh
-rw-rw-r-- 1 atompi atompi 508M Jan  2 20:38 Anaconda2-5.0.1-Linux-x86_64.sh
➜  ~ touch Anaconda2-5.0.1-Linux-x86_64.sh
➜  ~ ll Anaconda2-5.0.1-Linux-x86_64.sh
-rw-rw-r-- 1 atompi atompi 508M Jan  2 20:39 Anaconda2-5.0.1-Linux-x86_64.sh
➜  ~ date
Sat Jan  2 20:39:31 PST 2018
```
## 将文件改为任意时间

``` plain
➜  ~ ll Downloads/Anaconda2-5.0.1-Linux-x86_64.sh
-rw-rw-r-- 1 atompi atompi 508M Jan  2 20:38 Downloads/Anaconda2-5.0.1-Linux-x86_64.sh
➜  ~ touch -t 201801060354 Downloads/Anaconda2-5.0.1-Linux-x86_64.sh
➜  ~ ll Downloads/Anaconda2-5.0.1-Linux-x86_64.sh
-rw-rw-r-- 1 atompi atompi 508M Jan  6 03:54 Downloads/Anaconda2-5.0.1-Linux-x86_64.sh
```
## 创建空文件

``` plain
➜  ~ ll ~/Downloads
total 540M
-rw-rw-r--  1 atompi atompi 508M Jan  1 20:38 Anaconda2-5.0.1-Linux-x86_64.sh
drwxr-xr-x 36 atompi atompi 4.0K Jan  5 05:59 mysql-5.6.38
-rw-rw-r--  1 atompi atompi  31M Sep 13 06:58 mysql-5.6.38.tar.gz
drwxrwxr-x  6 atompi atompi 4.0K Sep 21 07:20 redis-3.2.11
-rw-rw-r--  1 atompi atompi 1.5M Sep 21 07:20 redis-3.2.11.tar.gz
➜  ~ touch ~/Downloads/test.txt
➜  ~ ll ~/Downloads
total 540M
-rw-rw-r--  1 atompi atompi 508M Jan  1 20:38 Anaconda2-5.0.1-Linux-x86_64.sh
drwxr-xr-x 36 atompi atompi 4.0K Jan  5 05:59 mysql-5.6.38
-rw-rw-r--  1 atompi atompi  31M Sep 13 06:58 mysql-5.6.38.tar.gz
drwxrwxr-x  6 atompi atompi 4.0K Sep 21 07:20 redis-3.2.11
-rw-rw-r--  1 atompi atompi 1.5M Sep 21 07:20 redis-3.2.11.tar.gz
-rw-rw-r--  1 atompi atompi    0 Jan  6 04:04 test.txt
```
