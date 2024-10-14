---
title: 每天一个 Linux 命令（35） -- updatedb
date: 2018/02/04
tags: Linux
description: Some old articles may have been deprecated.
---

创建或更新slocate命令所必需的数据库文件

## 补充说明

**updatedb命令** 用来创建或更新slocate命令所必需的数据库文件。updatedb命令的执行过程较长，因为在执行时它会遍历整个系统的目录树，并将所有的文件信息写入slocate数据库文件中。

补充说明 2：slocate本身具有一个数据库，里面存放了系统中文件与目录的相关信息。

## 语法

``` plain
updatedb (选项)
```
## 选项

``` plain
-o&lt;文件&gt;：忽略默认的数据库文件，使用指定的slocate数据库文件；
-U&lt;目录&gt;：更新指定目录的slocate数据库；
-v：显示执行的详细过程。
```
## 实例

实用updatedb命令的`-U`选项可以指定要更新slocate数据库的目录。

``` plain
updatedb -U /usr/local/  更新指定命令的slocate数据库
```
