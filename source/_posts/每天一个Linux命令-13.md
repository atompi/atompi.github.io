---
title: 每天一个 Linux 命令（13） -- less
date: 2018/01/13
tags: Linux
description: Some old articles may have been deprecated.
---

## 逐屏查看文本文件

<th align="center">按键命令</th><th align="center">操作</th>
|------
<td align="center">PageDn 、 e 或空格</td><td align="center">向下翻页</td>
<td align="center">PageUp 或 b</td><td align="center">向上翻页</td>
<td align="center">Enter 、 e 、 j 或下箭头键</td><td align="center">向下一行</td>
<td align="center">y 、 k 、 或上箭头键</td><td align="center">向上一行</td>
<td align="center">G 或 p</td><td align="center">跳到文件尾部</td>
<td align="center">1G</td><td align="center">跳到文件开头</td>
<td align="center">q</td><td align="center">退出less</td>
<td align="center">=</td><td align="center">显示文件信息</td>
<td align="center">v</td><td align="center">编辑当前文件</td>
<td align="center">/</td><td align="center">搜索</td>
<td align="center">n</td><td align="center">向前搜索</td>
<td align="center">N</td><td align="center">向后搜索</td>

``` plain
root@atompi:~# less 每天一个Linux命令-12.md
---
title: 每天一个 Linux 命令（12） -- cat
date: 2018-01-12 21:45:15
tags:
  - Linux
  - shell
categories:
  - Linux
---

## 在标准输出设备上查看文件内容

root@atompi:~# cat 每天一个Linux命令-11.md
---
title: 每天一个 Linux 命令（11） -- file
date: 2018-01-11 21:31:34
tags:
  - Linux
  - shell
categories:
  - Linux
---

## 查看文件类型

<!--more-->

## 同时查看多个文件（多个文件拼接输出）

root@atompi:~# echo "test1" &gt; test1
atompi.io/source/_posts/每天一个Linux命令-12.md
```
## 在 less 下进入编辑文件时，指定编辑器：

使用 less 前：

``` plain
export EDITOR=vim
```
