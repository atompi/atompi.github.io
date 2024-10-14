---
title: CentOS Ubuntu 离线源制作
date: 2018/12/20
tags: Linux
description: Some old articles may have been deprecated.
---

## 制作离线 yum 源

**在可以连接外网的服务器上操作**

### 修改源为ustc

``` plain
sudo mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
sudo vim /etc/yum.repos.d/CentOS-Base.repo
```
``` plain
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever - Base
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&amp;arch=$basearch&amp;repo=os
baseurl=https://mirrors.ustc.edu.cn/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#released updates
[updates]
name=CentOS-$releasever - Updates
# mirrorlist=http://mirrorlist.centos.org/?release=$releasever&amp;arch=$basearch&amp;repo=updates
baseurl=https://mirrors.ustc.edu.cn/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
# mirrorlist=http://mirrorlist.centos.org/?release=$releasever&amp;arch=$basearch&amp;repo=extras
baseurl=https://mirrors.ustc.edu.cn/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
# mirrorlist=http://mirrorlist.centos.org/?release=$releasever&amp;arch=$basearch&amp;repo=centosplus
baseurl=https://mirrors.ustc.edu.cn/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```
``` plain
sudo yum makecache
```
### 创建yum源安装包目录

``` plain
mkdir /tmp/yumcaches
```
### 安装 createrepo 和 epel-release

``` plain
yum install -y createrepo
sudo yum -y install epel-release
```
### yumdownloader 下载相关repo包

``` plain
cd /tmp/repodir
yumdownloader docker mysql mysql-devel java-1.8.0-openjdk python python-devel python-pip MySQL-python python-docker-py unzip iptables curl iproute ufw vim postfix --resolve
```
### createrepo 创建索引

``` plain
createrepo -v /tmp/repodir/
```
## 制作离线apt源

**在可以连接外网的服务器上操作**

### 修改源为阿里云

`vim /etc/apt/sources.list`

``` plain
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
```
`sudo apt-get update`

### 下载源安装包

``` plain
sudo apt install -y dpkg-dev

sudo apt-get -d install openjdk-8-jdk libltdl7 mysql-client python python-dev python-pip python-mysqldb unzip iptables curl iproute ufw vim python-apt postfix
```
### copy /var/cache/apt/archives/* 到 /home/atompi/localpacks/ 下

``` plain
mkdir -p /home/atompi/localpacks

sudo cp -rf /var/cache/apt/archives/* /home/atompi/localpacks/
```
### 创建依赖索引

``` plain
sudo dpkg-scanpackages localpacks /dev/null | gzip &gt; /home/atompi/localpacks/Packages.gz
```
### copy 到目标机器

``` plain
scp -r /home/atompi/localpacks atompi@192.168.1.131:/tmp/
```
### 应用配置

`sudo vi /etc/apt/sources.list`

``` plain
deb file:///tmp/ localpacks/
```
`sudo vi /etc/apt/apt.conf`

``` plain
APT { Get { AllowUnauthenticated "1"; }; };
```
### 更新源

``` plain
sudo apt-get update
```
## 制作 pip 源

**在可以连接外网的服务器上操作**

### 安装python、python-dev、python-pip

``` plain
sudo apt-get install -y python python-dev python-pip
```
### pip 安装 pip2pi

``` plain
pip install pip2pi
```
### 制作一个 requirements.txt 文件

`vim /home/atompi/requirements.txt`

``` plain
docker-py
```
### 准备一个 packages 文件夹，作为存放本地源的路径

``` plain
mkdir -p /home/atompi/pipackages
```
### 下载 pip 包

``` plain
pip2pi /home/atompi/pipackages --no-use-wheel -r requirements.txt
```
### 复制 packages 目录和 requirements.txt 文件到不能连接外网的目标机器上

``` plain
scp -r /home/atompi/pipackages atompi@192.168.1.131:/tmp/
scp /home/atompi/requirements.txt atompi@192.168.1.131:/tmp/
```
### 开始离线安装

``` plain
pip install --no-index --find-links=file:///tmp/pipackages/ -r /tmp/requirements.txt
```
``` plain
最后，需要在gitlab容器中激活git用户

passwd git
```
