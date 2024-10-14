---
title: 每天一个 Linux 命令（14） -- head
date: 2018/01/14
tags: Linux
description: Some old articles may have been deprecated.
---

## 查看文件前 10 行内容

``` plain
root@atompi:~# head 每天一个Linux命令-13.md
---
title: 每天一个 Linux 命令（13） -- less
date: 2018-01-13 21:52:54
tags:
  - Linux
  - shell
categories:
  - Linux
---
```
## 查看多个文件前 10 行内容

``` plain
root@atompi:~# head 每天一个Linux命令-12.md 每天一个Linux命令-13.md
==&gt; 每天一个Linux命令-12.md &lt;==
---
title: 每天一个 Linux 命令（12） -- cat
date: 2018-01-12 21:45:15
tags:
  - Linux
  - shell
categories:
  - Linux
---


==&gt; 每天一个Linux命令-13.md &lt;==
---
title: 每天一个 Linux 命令（13） -- less
date: 2018-01-13 21:52:54
tags:
  - Linux
  - shell
categories:
  - Linux
---
```
## 查看文件前 n 行

``` plain
root@atompi:~# head -n 3 每天一个Linux命令-13.md
---
title: 每天一个 Linux 命令（13） -- less
date: 2018-01-13 21:52:54
```
## 查看文件前 c 字节内容

``` plain
root@atompi:~# head -c 3 每天一个Linux命令-13.md
---

root@atompi:~# head -c 10 每天一个Linux命令-13.md
---
title:

root@atompi:~# head -c 100 每天一个Linux命令-13.md
---
title: 每天一个 Linux 命令（13） -- less
date: 2018-01-13 21:52:54
tags:
  - Linux
  - s

root@atompi:~# head -c 1KB 每天一个Linux命令-13.md
---
title: 每天一个 Linux 命令（13） -- less
date: 2018-01-13 21:52:54
tags:
  - Linux
  - shell
categories:
  - Linux
---

## 逐屏查看文本文件

|按键命令|操作|
|:--:|:--:|
|PageDn 、 e 或空格|向下翻页|
|PageUp 或 b|向上翻页|
|Enter 、 e 、 j 或下箭头键|向下一行|
|y 、 k 、 或上箭头键|向上一行|
|G 或 p|跳到文件尾部|
|1G|跳到文件开头|
|q|退出less|
|=|显示文件信息|
|v|编辑当前文件|
|/|搜索|
|n|向前搜索|
|N|向后搜索|

<!--more-->

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

## 同时查看多个文
```
