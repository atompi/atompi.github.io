---
title: pip 使用国内镜像源安装依赖包
date: 2018/01/05
tags: python
description: Some old articles may have been deprecated.
---

## 全局配置目录

在 linux 默认的路径是 : `$HOME/.pip/pip.conf`

在 windows 默认的路径是 : `%HOME%\pip\pip.ini`

## 全局配置国内镜像源

默认路径下并不存在配置文件，需要新建，然后在配置文件中写入：

``` plain
[global]
index-url = http://pypi.mirrors.ustc.edu.cn/simple/
```
即可修改镜像源，等号之后的部分就是镜像源的地址。

## 推荐的国内镜像源

``` plain
阿里云 : http://mirrors.aliyun.com/pypi/simple/
中国科技大学 : https://pypi.mirrors.ustc.edu.cn/simple/
豆瓣 : http://pypi.douban.com/simple/
中国科学院 : http://pypi.mirrors.opencas.cn/simple/
清华大学 : https://pypi.tuna.tsinghua.edu.cn/simple/
```
## 临时使用镜像源

临时使用镜像源很简单，用-i指定就行了，例如：

``` plain
sudo easy_install ansible -i http://pypi.douban.com/simple --trusted-host pypi.douban.com
sudo pip install ansible -i http://pypi.douban.com/simple --trusted-host pypi.douban.com
```
