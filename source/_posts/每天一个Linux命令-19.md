---
title: 每天一个 Linux 命令（19） -- uniq
date: 2018/01/19
tags: Linux
description: Some old articles may have been deprecated.
---

报告或忽略文件中的重复行

## 补充说明

**uniq命令** 用于报告或忽略文件中的重复行，一般与sort命令结合使用。

## 语法

``` plain
uniq (选项) (参数)
```
## 选项

``` plain
-c或——count：在每列旁边显示该行重复出现的次数；
-d或--repeated：仅显示重复出现的行列；
-f&lt;栏位&gt;或--skip-fields=&lt;栏位&gt;：忽略比较指定的栏位；
-s&lt;字符位置&gt;或--skip-chars=&lt;字符位置&gt;：忽略比较指定的字符；
-u或——unique：仅显示出一次的行列；
-w&lt;字符位置&gt;或--check-chars=&lt;字符位置&gt;：指定要比较的字符。
```
## 参数

- 输入文件：指定要去除的重复行文件。如果不指定此项，则从标准读取数据；
- 输出文件：指定要去除重复行后的内容要写入的输出文件。如果不指定此选项，则将内容显示到标准输出设备（显示终端）。

## 实例

删除重复行：

``` plain
uniq file.txt
sort file.txt | uniq
sort -u file.txt
```
只显示单一行：

``` plain
uniq -u file.txt
sort file.txt | uniq -u
```
统计各行在文件中出现的次数：

``` plain
sort file.txt | uniq -c
```
在文件中找出重复的行：

``` plain
sort file.txt | uniq -d
```
