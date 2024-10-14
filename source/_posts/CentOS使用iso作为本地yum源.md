---
title: CentOS 使用 iso 作为本地 yum 源
date: 2018/01/15
tags: Linux
description: Some old articles may have been deprecated.
---

## 用本地 iso 文件创建本地 yum 源

1. 创建 iso 存放目录和挂载目录

``` plain
mkdir /mnt/iso
mkdir /mnt/isorom
```
<li>将 iso 镜像文件上传到 `/mnt/iso` 文件夹下
</li>
<li>将 `/mnt/iso/` 下的 iso 文件挂载到 `/mnt/isorom` 目录
</li>

``` plain
cd /mnt/iso
mount -o loop /mnt/iso/CentOS-7-x86_64-DVD-1708.iso /mnt/isorom
```
1. 修改 `*.repo` 文件

如果 `/etc/yum.repos.d/` 下面有其它的 `*.repo` 文件，先创建个文件夹，将这些 `*.repo` 先转移到文件夹中，自己写一个 `local.repo` 的文件

``` plain
mkdir ~/repobak
mv /etc/yum.repos.d/* ~/repobak/
vim /etc/yum.repos.d/local.repo
```
`local.repo` 内容如下：

``` plain
[base]
name=CentOS7-DVD-ISO
# 注：这里的 baseurl 就是你挂载的目录，在这里是 /mnt/isorom
baseurl=file:///mnt/isorom
# 注：这里的值 enabled 一定要为 1
enabled=1
# gpgckeck的值无所谓
gpgckeck=0
# 注：这个你cd /mnt/isorom/可以看到这个key，这里仅仅是个例子
gpgkey=file:///mnt/isorom/RPM-GPG-KEY-CentOS-7
```
## 清除 yum 缓存，建立缓存，开始正常使用 iso 源

``` plain
yum clean all
yum makecache
yum install -y vim
```
