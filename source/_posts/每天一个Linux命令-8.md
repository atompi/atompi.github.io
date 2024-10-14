---
title: 每天一个 Linux 命令（8） -- man
date: 2018/01/08
tags: Linux
description: Some old articles may have been deprecated.
---

## 使用 man 查找命令相关信息：命令的手册

使用 `man name` 来查看命令的使用方法、介绍等相关信息，对学习一个命令非常有帮助。

``` plain
root@atompi:~# man man
# 进入如下状态，按 'q' 退出
MAN(1)                                               Manual pager utils                                              MAN(1)

NAME
       man - an interface to the on-line reference manuals

SYNOPSIS
       man  [-C file] [-d] [-D] [--warnings[=warnings]] [-R encoding] [-L locale] [-m system[,...]] [-M path] [-S list] [-e
       extension] [-i|-I] [--regex|--wildcard] [--names-only] [-a] [-u] [--no-subpages] [-P pager]  [-r  prompt]  [-7]  [-E
       encoding]  [--no-hyphenation] [--no-justification] [-p string] [-t] [-T[device]] [-H[browser]] [-X[dpi]] [-Z] [[sec‐
       tion] page ...] ...
       man -k [apropos options] regexp ...
       man -K [-w|-W] [-S list] [-i|-I] [--regex] [section] term ...
       man -f [whatis options] page ...
       man -l [-C file] [-d] [-D] [--warnings[=warnings]] [-R encoding] [-L locale] [-P pager] [-r prompt] [-7] [-E  encod‐
       ing] [-p string] [-t] [-T[device]] [-H[browser]] [-X[dpi]] [-Z] file ...
       man -w|-W [-C file] [-d] [-D] page ...
       man -c [-C file] [-d] [-D] page ...
       man [-?V]

DESCRIPTION
       man  is  the  system's  manual pager.  Each page argument given to man is normally the name of a program, utility or
       function.  The manual page associated with each of these arguments is then found and displayed.  A section, if  pro‐
       vided,  will  direct  man to look only in that section of the manual.  The default action is to search in all of the
       available sections following a pre-defined order ("1 n l 8 3 2 3posix 3pm 3perl 5 4 9 6 7" by default, unless  over‐
       ridden  by the SECTION directive in /etc/manpath.config), and to show only the first page found, even if page exists
       in several sections.

       The table below shows the section numbers of the manual followed by the types of pages they contain.

       1   Executable programs or shell commands
       2   System calls (functions provided by the kernel)
       3   Library calls (functions within program libraries)
       4   Special files (usually found in /dev)
       5   File formats and conventions eg /etc/passwd
       6   Games
 Manual page man(1) line 1 (press h for help or q to quit)
```
## 知道命令名称，如何快速查找其功能？

``` plain
root@atompi:~# man -f man
man (1)              - an interface to the on-line reference manuals
man (7)              - macros to format man pages
```
``` plain
root@atompi:~# whatis man
man (1)              - an interface to the on-line reference manuals
man (7)              - macros to format man pages
```
## 知道命令的功能，如何查找命令名称？

``` plain
man -k list
apropos -e list
# 以上两个命令都会输出很多匹配 `list` 的结果，输出的结果可能不全是想要的，但是，当你一时忘记了命令的全名，不妨试一试，说不定看到提示，你就想起来了呢。
# man -k list | grep list == apropos -e list
```
## 读取命令的特定手册页面

`man [1-8]`

[这 ‘1-8’ 是什么意思？](http://zheming.wang/blog/2011/02/17/65C9E90A-8D99-46DA-AC8D-F4D4EC825CD8/)

``` plain
root@atompi:~# man 5 passwd
PASSWD(5)                                       File Formats and Conversions                                      PASSWD(5)

NAME
       passwd - the password file

DESCRIPTION
       /etc/passwd contains one line for each user account, with seven fields delimited by colons (“:”). These fields are:

       ·   login name

       ·   optional encrypted password

       ·   numerical user ID

       ·   numerical group ID

       ·   user name or comment field

       ·   user home directory

       ·   optional user command interpreter

       The encrypted password field may be blank, in which case no password is required to authenticate as the specified
       login name. However, some applications which read the /etc/passwd file may decide not to permit any access at all if
       the password field is blank. If the password field is a lower-case “x”, then the encrypted password is actually
       stored in the shadow(5) file instead; there must be a corresponding line in the /etc/shadow file, or else the user
       account is invalid. If the password field is any other string, then it will be treated as an encrypted password, as
       specified by crypt(3).

       The comment field is used by various system utilities, such as finger(1).

       The home directory field provides the name of the initial working directory. The login program uses this information
       to set the value of the $HOME environmental variable.

       The command interpreter field provides the name of the user's command language interpreter, or the name of the
 Manual page passwd(5) line 1 (press h for help or q to quit)
```
## 提供几个有助于理解 man 的链接：

[https://www.linux.org/docs/](https://www.linux.org/docs/)

[https://linux.die.net/man/](https://linux.die.net/man/)

[http://www.linfo.org/man.html](http://www.linfo.org/man.html)

[http://man7.org/linux/man-pages/](http://man7.org/linux/man-pages/)
