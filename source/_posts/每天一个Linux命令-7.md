---
title: 每天一个 Linux 命令（7） -- rm
date: 2018/01/07
tags: Linux
description: Some old articles may have been deprecated.
---

## 删除文件

``` plain
➜  ~ ll
total 40K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
-rw-rw-r--  1 atompi atompi    2 Jan  6 04:26 test.txt
➜  ~ rm test.txt
➜  ~ ll
total 36K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
```
## 删除时提示，防止删除关键文件

``` plain
➜  ~ ll
total 36K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
drwxrwxr-x  5 atompi atompi 4.0K Jan  6 19:07 atomsrc
drwxrwxr-x  3 atompi atompi 4.0K Dec 28 20:26 Documents
drwxrwxr-x  6 atompi atompi 4.0K Jan  6 04:28 Downloads
drwxrwxr-x  4 atompi atompi 4.0K Jan  5 05:27 gitee
drwxrwxr-x  8 atompi atompi 4.0K Jan  3 20:25 mayun-ee-p
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 18:50 shadowsocks
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 05:26 Templates
-rw-rw-r--  1 atompi atompi    0 Jan  6 04:23 test-copy.txt
drwxrwxr-x  3 atompi atompi 4.0K Jan  6 04:36 testdir-home
➜  ~ rm -i test-copy.txt
rm: remove regular empty file 'test-copy.txt'? no
➜  ~ ll
total 36K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
drwxrwxr-x  5 atompi atompi 4.0K Jan  6 19:07 atomsrc
drwxrwxr-x  3 atompi atompi 4.0K Dec 28 20:26 Documents
drwxrwxr-x  6 atompi atompi 4.0K Jan  6 04:28 Downloads
drwxrwxr-x  4 atompi atompi 4.0K Jan  5 05:27 gitee
drwxrwxr-x  8 atompi atompi 4.0K Jan  3 20:25 mayun-ee-p
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 18:50 shadowsocks
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 05:26 Templates
-rw-rw-r--  1 atompi atompi    0 Jan  6 04:23 test-copy.txt
drwxrwxr-x  3 atompi atompi 4.0K Jan  6 04:36 testdir-home
```
## 删除空目录

``` plain
➜  ~ ll
total 36K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
drwxrwxr-x  5 atompi atompi 4.0K Jan  6 19:07 atomsrc
drwxrwxr-x  3 atompi atompi 4.0K Dec 28 20:26 Documents
drwxrwxr-x  6 atompi atompi 4.0K Jan  6 04:28 Downloads
drwxrwxr-x  4 atompi atompi 4.0K Jan  5 05:27 gitee
drwxrwxr-x  8 atompi atompi 4.0K Jan  3 20:25 mayun-ee-p
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 18:50 shadowsocks
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 05:26 Templates
-rw-rw-r--  1 atompi atompi    0 Jan  6 04:23 test-copy.txt
drwxrwxr-x  3 atompi atompi 4.0K Jan  6 04:36 testdir-home
➜  ~ cd testdir-home
➜  testdir-home ll
total 4.0K
drwxrwxr-x 2 atompi atompi 4.0K Jan  6 04:31 testdir-home-2
➜  testdir-home ll testdir-home-2
total 0
➜  testdir-home rm testdir-home-2
rm: cannot remove 'testdir-home-2': Is a directory
➜  testdir-home rmdir testdir-home-2
➜  testdir-home ll
total 0
```
## 删除文件和非空目录

``` plain
➜  ~ ll testdir
total 0
-rw-rw-r-- 1 atompi atompi 0 Jan  6 04:23 test-copy.txt
➜  ~ rmdir testdir
rmdir: failed to remove 'testdir': Directory not empty
➜  ~ rm -rf testdir
➜  ~ ll
total 32K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
drwxrwxr-x  5 atompi atompi 4.0K Jan  6 19:07 atomsrc
drwxrwxr-x  3 atompi atompi 4.0K Dec 28 20:26 Documents
drwxrwxr-x  6 atompi atompi 4.0K Jan  6 04:28 Downloads
drwxrwxr-x  4 atompi atompi 4.0K Jan  5 05:27 gitee
drwxrwxr-x  8 atompi atompi 4.0K Jan  3 20:25 mayun-ee-p
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 18:50 shadowsocks
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 05:26 Templates
```
## 删除文件名开头特殊的文件

比如以 `-` 开头的文件或文件夹，操作之前需要在文件名前加 `--`

``` plain
➜  ~ touch -- -testfile.txt
➜  ~ ll
total 32K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
drwxrwxr-x  5 atompi atompi 4.0K Jan  6 19:07 atomsrc
drwxrwxr-x  3 atompi atompi 4.0K Dec 28 20:26 Documents
drwxrwxr-x  6 atompi atompi 4.0K Jan  6 04:28 Downloads
drwxrwxr-x  4 atompi atompi 4.0K Jan  5 05:27 gitee
drwxrwxr-x  8 atompi atompi 4.0K Jan  3 20:25 mayun-ee-p
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 18:50 shadowsocks
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 05:26 Templates
-rw-rw-r--  1 atompi atompi    0 Jan  7 04:15 -testfile.txt
➜  ~
➜  ~
➜  ~ rm -testfile.txt
rm: invalid option -- 't'
Try 'rm ./-testfile.txt' to remove the file '-testfile.txt'.
Try 'rm --help' for more information.
➜  ~
➜  ~ rm -- -testfile.txt
➜  ~ ll
total 32K
drwxrwxr-x 22 atompi atompi 4.0K Jan  5 05:42 anaconda2
drwxrwxr-x  5 atompi atompi 4.0K Jan  6 19:07 atomsrc
drwxrwxr-x  3 atompi atompi 4.0K Dec 28 20:26 Documents
drwxrwxr-x  6 atompi atompi 4.0K Jan  6 04:28 Downloads
drwxrwxr-x  4 atompi atompi 4.0K Jan  5 05:27 gitee
drwxrwxr-x  8 atompi atompi 4.0K Jan  3 20:25 mayun-ee-p
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 18:50 shadowsocks
drwxrwxr-x  2 atompi atompi 4.0K Jan  5 05:26 Templates
```
