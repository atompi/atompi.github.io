---
title: 每天一个 Linux 命令（10） -- whereis, which
date: 2018/01/10
tags: Linux
description: Some old articles may have been deprecated.
---

## 查找命令相关路径 whereis

### 依次列出源文件、可执行文件、手册页文件

``` plain
root@atompi:~# whereis ls
ls: /bin/ls /usr/share/man/man1/ls.1.gz
```
### 只列出可执行文件

``` plain
root@atompi:~# whereis -b ls
ls: /bin/ls
```
### 只列出手册页文件

``` plain
root@atompi:~# whereis -m ls
ls: /usr/share/man/man1/ls.1.gz
```
## 查找命令被执行时的可执行文件路径 which

``` plain
root@atompi:~# which ls
/bin/ls
```
