---
title: Linux 修改用户名
date: 2018/01/11
tags: Linux
description: Some old articles may have been deprecated.
---

## usermod 修改用户名

退出当前用户，切换到 root 用户下

``` plain
exit # 不能直接使用 su 切换，这样源用户还会开启一个进程，
```
## 修改用户名 atompi 为 git

``` plain
usermod -l git atompi
```
## 修改 home 目录

``` plain
vim /etc/passwd

# 修改前：
git:x:1000:1000:UbuntuServer,,,:/home/atompi:/bin/bash

修改后：
git:x:1000:1000:UbuntuServer,,,:/home/git:/bin/bash
```
## 重命名原 home 目录

``` plain
mv /home/atompi /home/git
```
## 修改 group

此时 git 用户所在用户组仍然是 atompi ，通过修改 `/etc/group` 文件，修改用户组

``` plain
sudo vim /etc/group

# 修改前
atompi:x:1000:

# 修改后
git:x:1000:
```
## 修改完成，验证、重新登录

验证

``` plain
root@frontend:~# id atompi
id: ‘atompi’: no such user
root@frontend:~# id git
uid=1000(git) gid=1000(git) groups=1000(git),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),114(lpadmin),115(sambashare)
```
退出重新登陆

``` plain
exit
```
