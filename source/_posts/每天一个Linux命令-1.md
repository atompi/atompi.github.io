---
title: 每天一个 Linux 命令（1） -- 导航文件系统
date: 2018/01/01
tags: Linux
description: Some old articles may have been deprecated.
---

## 导航文件系统

### 列出当前目录的文件和文件夹

``` plain
➜  ~ ls
anaconda2  atomsrc  Documents  Downloads  Templates
```
### 列出指定目录的文件和文件夹

``` plain
➜  ~ ls Downloads
Anaconda2-5.0.1-Linux-x86_64.sh  mysql-5.6.38  mysql-5.6.38.tar.gz  redis-3.2.11  redis-3.2.11.tar.gz
```
### 使用通配符列出文件夹内容

``` plain
➜  ~ ls ~/Downloads/*.tar.gz
/home/atompi/Downloads/mysql-5.6.38.tar.gz  /home/atompi/Downloads/redis-3.2.11.tar.gz
```
### 查看子文件夹中的文件列表

``` plain
➜  ~ ls -R ~/Documents
/home/atompi/Documents:
Templates

/home/atompi/Documents/Templates:
DockerFiles

/home/atompi/Documents/Templates/DockerFiles:
dockerfiles  docker_images

/home/atompi/Documents/Templates/DockerFiles/dockerfiles:
IDEA-licsrv

/home/atompi/Documents/Templates/DockerFiles/dockerfiles/IDEA-licsrv:
Dockerfile  linux.tar.gz  README.md

/home/atompi/Documents/Templates/DockerFiles/docker_images:
idealicsrv100.tar
```
### 以单独一列显示内容列表

``` plain
➜  ~ ls -1 ~/Downloads
Anaconda2-5.0.1-Linux-x86_64.sh
mysql-5.6.38
mysql-5.6.38.tar.gz
redis-3.2.11
redis-3.2.11.tar.gz
```
### 以逗号分隔列表显示内容

``` plain
➜  ~ ls -m ~/Downloads
Anaconda2-5.0.1-Linux-x86_64.sh, mysql-5.6.38, mysql-5.6.38.tar.gz, redis-3.2.11, redis-3.2.11.tar.gz
```
### 查看隐藏的文件和文件夹

``` plain
➜  ~ ls -a ~/Downloads
.  ..  Anaconda2-5.0.1-Linux-x86_64.sh  mysql-5.6.38  mysql-5.6.38.tar.gz  redis-3.2.11  redis-3.2.11.tar.gz
```
### 直观显示文件的类型

``` plain
➜  ~ ls -F /usr/local/bin
ansible*             ansible-console@  ansible-inventory@  ansible-vault@    redis-check-aof*  redis-sentinel@  ssserver*
ansible-config@      ansible-doc@      ansible-playbook@   proxy*            redis-check-rdb*  redis-server*    startss.sh*
ansible-connection*  ansible-galaxy@   ansible-pull@       redis-benchmark*  redis-cli*        sslocal*         virtualenv*
```
### 列出权限、所有者等其他信息

``` plain
➜  ~ ls -l ~/Downloads
total 552872
-rw-rw-r--  1 atompi atompi 532375438 Oct 25 09:38 Anaconda2-5.0.1-Linux-x86_64.sh
drwxr-xr-x 36 atompi atompi      4096 Jan  5 05:59 mysql-5.6.38
-rw-rw-r--  1 atompi atompi  32197625 Sep 13 06:58 mysql-5.6.38.tar.gz
drwxrwxr-x  6 atompi atompi      4096 Sep 21 07:20 redis-3.2.11
-rw-rw-r--  1 atompi atompi   1550452 Sep 21 07:20 redis-3.2.11.tar.gz
```
### 以倒序列出文件夹内容

``` plain
➜  ~ ls -lr ~/Downloads
total 552872
-rw-rw-r--  1 atompi atompi   1550452 Sep 21 07:20 redis-3.2.11.tar.gz
drwxrwxr-x  6 atompi atompi      4096 Sep 21 07:20 redis-3.2.11
-rw-rw-r--  1 atompi atompi  32197625 Sep 13 06:58 mysql-5.6.38.tar.gz
drwxr-xr-x 36 atompi atompi      4096 Jan  5 05:59 mysql-5.6.38
-rw-rw-r--  1 atompi atompi 532375438 Oct 25 09:38 Anaconda2-5.0.1-Linux-x86_64.sh
```
### 按日期和时间排序文件夹内容

``` plain
➜  ~ ls -latr ~/Downloads
total 552880
-rw-rw-r--  1 atompi atompi  32197625 Sep 13 06:58 mysql-5.6.38.tar.gz
drwxrwxr-x  6 atompi atompi      4096 Sep 21 07:20 redis-3.2.11
-rw-rw-r--  1 atompi atompi   1550452 Sep 21 07:20 redis-3.2.11.tar.gz
-rw-rw-r--  1 atompi atompi 532375438 Oct 25 09:38 Anaconda2-5.0.1-Linux-x86_64.sh
drwxr-xr-x 36 atompi atompi      4096 Jan  5 05:59 mysql-5.6.38
drwxrwxr-x  4 atompi atompi      4096 Jan  5 06:06 .
drwxr-xr-x 19 atompi atompi      4096 Jan  6 03:08 ..
```
### 按文件大小排序文件夹内容

``` plain
➜  ~ ls -laS ~/Downloads
total 552880
-rw-rw-r--  1 atompi atompi 532375438 Oct 25 09:38 Anaconda2-5.0.1-Linux-x86_64.sh
-rw-rw-r--  1 atompi atompi  32197625 Sep 13 06:58 mysql-5.6.38.tar.gz
-rw-rw-r--  1 atompi atompi   1550452 Sep 21 07:20 redis-3.2.11.tar.gz
drwxrwxr-x  4 atompi atompi      4096 Jan  5 06:06 .
drwxr-xr-x 19 atompi atompi      4096 Jan  6 03:10 ..
drwxr-xr-x 36 atompi atompi      4096 Jan  5 05:59 mysql-5.6.38
drwxrwxr-x  6 atompi atompi      4096 Sep 21 07:20 redis-3.2.11
```
### 用 K 、 M 、 G表示文件大小

``` plain
➜  ~ ls -laSh ~/Downloads
total 540M
-rw-rw-r--  1 atompi atompi 508M Oct 25 09:38 Anaconda2-5.0.1-Linux-x86_64.sh
-rw-rw-r--  1 atompi atompi  31M Sep 13 06:58 mysql-5.6.38.tar.gz
-rw-rw-r--  1 atompi atompi 1.5M Sep 21 07:20 redis-3.2.11.tar.gz
drwxrwxr-x  4 atompi atompi 4.0K Jan  5 06:06 .
drwxr-xr-x 19 atompi atompi 4.0K Jan  6 03:12 ..
drwxr-xr-x 36 atompi atompi 4.0K Jan  5 05:59 mysql-5.6.38
drwxrwxr-x  6 atompi atompi 4.0K Sep 21 07:20 redis-3.2.11
```
### 显示当前目录路径

``` plain
➜  ~ pwd
/home/atompi
```
### 更改当前目录

``` plain
➜  ~ pwd
/home/atompi
➜  ~ cd /usr/local/bin
➜  bin pwd
/usr/local/bin
```
### 回到之前的目录

``` plain
➜  ~ pwd
/home/atompi
➜  ~ cd /usr/local/bin
➜  bin pwd
/usr/local/bin
➜  bin cd -
~
➜  ~ pwd
/home/atompi
```
