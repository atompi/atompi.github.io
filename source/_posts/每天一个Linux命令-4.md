---
title: 每天一个 Linux 命令（4） -- cp
date: 2018/01/04
tags: Linux
description: Some old articles may have been deprecated.
---

## 复制文件

``` plain
➜  ~ touch test.txt
➜  ~ cp test.txt test-copy.txt
➜  ~ cp test.txt ~/Downloads
➜  ~ ll ~/Downloads
total 540M
-rw-rw-r--  1 atompi atompi 508M Jan  1 20:38 Anaconda2-5.0.1-Linux-x86_64.sh
drwxr-xr-x 36 atompi atompi 4.0K Jan  5 05:59 mysql-5.6.38
-rw-rw-r--  1 atompi atompi  31M Sep 13 06:58 mysql-5.6.38.tar.gz
drwxrwxr-x  6 atompi atompi 4.0K Sep 21 07:20 redis-3.2.11
-rw-rw-r--  1 atompi atompi 1.5M Sep 21 07:20 redis-3.2.11.tar.gz
drwxrwxr-x  3 atompi atompi 4.0K Jan  6 04:19 testdir
-rw-rw-r--  1 atompi atompi    0 Jan  6 04:23 test.txt
```
## 显示复制的文件

``` plain
➜  ~ cp -v test.txt ~/Downloads
'test.txt' -&gt; '/home/atompi/Downloads/test.txt'
```
## 覆盖时提示，以防覆盖重要文件

``` plain
➜  ~ echo "1" &gt; test.txt
➜  ~ echo "2" &gt; ~/Downloads/test.txt
➜  ~ cat test.txt
1
➜  ~ cp -i test.txt ~/Downloads
cp: overwrite '/home/atompi/Downloads/test.txt'? no
➜  ~ cat ~/Downloads/test.txt
2
```
## 递归复制目录

``` plain
➜  ~ cp testdir-home ~/Downloads/testdir-Downloads
cp: omitting directory 'testdir-home'
➜  ~ cp -r testdir-home ~/Downloads/testdir-Downloads
➜  ~ ll ~/Downloads/testdir-Downloads
total 4.0K
drwxrwxr-x 2 atompi atompi 4.0K Jan  6 04:28 testdir-home

➜  ~ mkdir testdir-home/testdir-home-2
➜  ~ cp -R testdir-home ~/Downloads/testdir/
➜  ~ ll Downloads/testdir
total 8.0K
drwxrwxr-x 3 atompi atompi 4.0K Jan  6 04:19 testdir1
drwxrwxr-x 3 atompi atompi 4.0K Jan  6 04:31 testdir-home
➜  ~ ll Downloads/testdir/testdir-home
total 4.0K
drwxrwxr-x 2 atompi atompi 4.0K Jan  6 04:31 testdir-home-2
```
