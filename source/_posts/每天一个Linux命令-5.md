---
title: 每天一个 Linux 命令（5） -- mv
date: 2018/01/05
tags: Linux
description: Some old articles may have been deprecated.
---

## 重命名文件、文件夹

``` plain
➜  ll
total 4.0K
drwxrwxr-x 2 atompi atompi 4.0K Jan  6 04:31 testdir-home-2
➜  mv testdir-home-1 testdir-home-2
➜  ll
total 4.0K
drwxrwxr-x 2 atompi atompi 4.0K Jan  6 04:31 testdir-home-2
```
## 移动文件、文件夹

``` plain
➜  ~ cd testdir-home
➜  testdir-home ll
total 4.0K
drwxrwxr-x 2 atompi atompi 4.0K Jan  6 04:31 testdir-home-2
➜  testdir-home mv testdir-home-2 ~/
➜  testdir-home ll
total 0
➜  testdir-home ll ~/
total 44K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
drwxrwxr-x  5 atompi atompi 4.0K Jan  5 22:02 atomsrc
drwxrwxr-x  3 atompi atompi 4.0K Dec 28 20:26 Documents
drwxrwxr-x  6 atompi atompi 4.0K Jan  6 04:28 Downloads
drwxrwxr-x  4 atompi atompi 4.0K Jan  5 05:27 gitee
drwxrwxr-x  8 atompi atompi 4.0K Jan  3 20:25 mayun-ee-p
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 18:50 shadowsocks
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 05:26 Templates
-rw-rw-r--  1 atompi atompi    0 Jan  6 04:23 test-copy.txt
drwxrwxr-x  2 atompi atompi 4.0K Jan  6 04:34 testdir-home
drwxrwxr-x  2 atompi atompi 4.0K Jan  6 04:31 testdir-home-2
-rw-rw-r--  1 atompi atompi    2 Jan  6 04:26 test.txt
```
