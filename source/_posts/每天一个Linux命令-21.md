---
title: 每天一个 Linux 命令（21） -- sed
date: 2018/01/21
tags: Linux
description: Some old articles may have been deprecated.
---

功能强大的流式文本编辑器

## 补充说明

**sed** 是一种流编辑器，它是文本处理中非常中的工具，能够完美的配合正则表达式使用，功能不同凡响。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有 改变，除非你使用重定向存储输出。Sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。

## sed的选项、命令、替换标记

 **命令格式**

``` plain
sed [options] 'command' file(s)
sed [options] -f scriptfile file(s)
```
## 选项

``` plain
-e<script>或--expression=<script>：以选项中的指定的script来处理输入的文本文件；<br>-f<script文件>或--file=<script文件>：以选项中指定的script文件来处理输入的文本文件；<br>-h或--help：显示帮助；<br>-n或--quiet或——silent：仅显示script处理后的结果；<br>-V或--version：显示版本信息。<br></pre></td>

## [](#参数)参数

文件：指定待处理的文本文件列表。

### [](#sed命令)sed命令
<td class="gutter"><pre>1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br>14<br>15<br>16<br>17<br>18<br>19<br>20<br>21<br>22<br>23<br>24<br>25<br></pre></td><td class="code"><pre>**a\**  在当前行下面插入文本。<br>**i\**  在当前行上面插入文本。<br>**c\**  把选定的行改为新的文本。<br>**d**  删除，删除选择的行。<br>**D**  删除模板块的第一行。<br>**s**  替换指定字符<br>**h**  拷贝模板块的内容到内存中的缓冲区。<br>**H**  追加模板块的内容到内存中的缓冲区。<br>**g**  获得内存缓冲区的内容，并替代当前模板块中的文本。<br>**G**  获得内存缓冲区的内容，并追加到当前模板块文本的后面。<br>**l**  列表不能打印字符的清单。<br>**n**  读取下一个输入行，用下一个命令处理新的行而不是用第一个命令。<br>**N**  追加下一个输入行到模板块后面并在二者间嵌入一个新行，改变当前行号码。<br>**p**  打印模板块的行。<br>**P** (大写) 打印模板块的第一行。<br>**q**  退出Sed。<br>**b lable**  分支到脚本中带有标记的地方，如果分支不存在则分支到脚本的末尾。<br>**r file**  从file中读行。<br>**t label**  if分支，从最后一行开始，条件一旦满足或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。<br>**T label**  错误分支，从最后一行开始，一旦发生错误或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。<br>**w file**  写并追加模板块到file末尾。  <br>**W file**  写并追加模板块的第一行到file末尾。  <br>**!**  表示后面的命令对所有没有被选定的行发生作用。  <br>**=**  打印当前行号码。  <br>**#**  把注释扩展到下一个换行符以前。<br></pre></td>

## [](#sed替换标记)sed替换标记
<td class="gutter"><pre>1<br>2<br>3<br>4<br>5<br>6<br>7<br></pre></td><td class="code"><pre>**g**  表示行内全面替换。  <br>**p**  表示打印行。  <br>**w**  表示把行写入一个文件。  <br>**x**  表示互换模板块中的文本和缓冲区中的文本。  <br>**y**  表示把一个字符翻译为另外的字符（但是不用于正则表达式）<br>**\1**  子串匹配标记<br>**&**  已匹配字符串标记<br></pre></td>

## [](#sed元字符集)sed元字符集
<td class="gutter"><pre>1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br>9<br>10<br>11<br>12<br>13<br></pre></td><td class="code"><pre>**^**  匹配行开始，如：/^sed/匹配所有以sed开头的行。<br>**$**  匹配行结束，如：/sed$/匹配所有以sed结尾的行。<br>**.**  匹配一个非换行符的任意字符，如：/s.d/匹配s后接一个任意字符，最后是d。<br>**** * 匹配0个或多个字符，如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。<br>**[]**  匹配一个指定范围内的字符，如/[ss]ed/匹配sed和Sed。  <br>**[^]**  匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。<br>**\(..\)**  匹配子串，保存匹配的字符，如s/\(love\)able/\1rs，loveable被替换成lovers。<br>**&**  保存搜索字符用来替换其他字符，如s/love/ **&** /，love这成 **love** 。<br>**\<**  匹配单词的开始，如:/\<love/匹配包含以love开头的单词的行。<br>**\>**  匹配单词的结束，如/love\>/匹配包含以love结尾的单词的行。<br>**x\{m\}**  重复字符x，m次，如：/0\{5\}/匹配包含5个0的行。<br>**x\{m,\}**  重复字符x，至少m次，如：/0\{5,\}/匹配至少有5个0的行。<br>**x\{m,n\}**  重复字符x，至少m次，不多于n次，如：/0\{5,10\}/匹配5~10个0的行。<br></pre></td>

## [](#sed用法实例)sed用法实例

### [](#替换操作：s命令)替换操作：s命令

替换文本中的字符串：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed 's/book/books/' file<br></pre></td>

 **-n选项** 和 **p命令** 一起使用表示只打印那些发生替换的行：

sed -n ‘s/test/TEST/p’ file

直接编辑文件 **选项-i** ，会匹配file文件中每一行的第一个book替换为books：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -i 's/book/books/g' file<br></pre></td>

### [](#全面替换标记g)全面替换标记g

使用后缀 /g 标记会替换每一行中的所有匹配：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed 's/book/books/g' file<br></pre></td>

当需要从第N处匹配开始替换时，可以使用 /Ng：
<td class="gutter"><pre>1<br>2<br>3<br>4<br>5<br>6<br>7<br>8<br></pre></td><td class="code"><pre>echo sksksksksksk | sed 's/sk/SK/2g'<br>skSKSKSKSKSK<br><br>echo sksksksksksk | sed 's/sk/SK/3g'<br>skskSKSKSKSK<br><br>echo sksksksksksk | sed 's/sk/SK/4g'<br>skskskSKSKSK<br></pre></td>

### [](#定界符)定界符

以上命令中字符 / 在sed中作为定界符使用，也可以使用任意的定界符：
<td class="gutter"><pre>1<br>2<br></pre></td><td class="code"><pre>sed 's:test:TEXT:g'<br>sed 's|test|TEXT|g'<br></pre></td>

定界符出现在样式内部时，需要进行转义：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed 's/\/bin/\/usr\/local\/bin/g'<br></pre></td>

### [](#删除操作：d命令)删除操作：d命令

删除空白行：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '/^$/d' file<br></pre></td>

删除文件的第2行：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '2d' file<br></pre></td>

删除文件的第2行到末尾所有行：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '2,$d' file<br></pre></td>

删除文件最后一行：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '$d' file<br></pre></td>

删除文件中所有开头是test的行：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '/^test/'d file<br></pre></td>

### [](#已匹配字符串标记-amp)已匹配字符串标记&

正则表达式 \w+ 匹配每一个单词，使用 [&] 替换它，& 对应于之前所匹配到的单词：
<td class="gutter"><pre>1<br>2<br></pre></td><td class="code"><pre>echo this is a test line | sed 's/\w\+/[&]/g'<br>[this] [is] [a] [test] [line]<br></pre></td>

所有以192.168.0.1开头的行都会被替换成它自已加localhost：
<td class="gutter"><pre>1<br>2<br></pre></td><td class="code"><pre>sed 's/^192.168.0.1/&localhost/' file<br>192.168.0.1localhost<br></pre></td>

### [](#子串匹配标记-1)子串匹配标记\1

匹配给定样式的其中一部分：
<td class="gutter"><pre>1<br>2<br></pre></td><td class="code"><pre>echo this is digit 7 in a number | sed 's/digit \([0-9]\)/\1/'<br>this is 7 in a number<br></pre></td>

命令中 digit 7，被替换成了 7。样式匹配到的子串是 7，(..) 用于匹配子串，对于匹配到的第一个子串就标记为  **\1** ，依此类推匹配到的第二个结果就是  **\2** ，例如：
<td class="gutter"><pre>1<br>2<br></pre></td><td class="code"><pre>echo aaa BBB | sed 's/\([a-z]\+\) \([A-Z]\+\)/\2 \1/'<br>BBB aaa<br></pre></td>

love被标记为1，所有loveable会被替换成lovers，并打印出来：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -n 's/\(love\)able/\1rs/p' file<br></pre></td>

### [](#组合多个表达式)组合多个表达式
<td class="gutter"><pre>1<br>2<br>3<br>4<br>5<br></pre></td><td class="code"><pre>sed '表达式' | sed '表达式'<br><br>等价于：<br><br>sed '表达式; 表达式'<br></pre></td>

### [](#引用)引用

sed表达式可以使用单引号来引用，但是如果表达式内部包含变量字符串，就需要使用双引号。
<td class="gutter"><pre>1<br>2<br>3<br></pre></td><td class="code"><pre>test=hello<br>echo hello WORLD | sed "s/$test/HELLO"<br>HELLO WORLD<br></pre></td>

### [](#选定行的范围：-（逗号）)选定行的范围：,（逗号）

所有在模板test和check所确定的范围内的行都被打印：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -n '/test/,/check/p' file<br></pre></td>

打印从第5行开始到第一个包含以test开始的行之间的所有行：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -n '5,/^test/p' file<br></pre></td>

对于模板test和west之间的行，每行的末尾用字符串aaa bbb替换：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '/test/,/west/s/$/aaa bbb/' file<br></pre></td>

### [](#多点编辑：e命令)多点编辑：e命令

-e选项允许在同一行里执行多条命令：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -e '1,5d' -e 's/test/check/' file<br></pre></td>

上面sed表达式的第一条命令删除1至5行，第二条命令用check替换test。命令的执行顺序对结果有影响。如果两个命令都是替换命令，那么第一个替换命令将影响第二个替换命令的结果。

和 -e 等价的命令是 –expression：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed --expression='s/test/check/' --expression='/love/d' file<br></pre></td>

### [](#从文件读入：r命令)从文件读入：r命令

file里的内容被读进来，显示在与test匹配的行后面，如果匹配多行，则file的内容将显示在所有匹配行的下面：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '/test/r file' filename<br></pre></td>

### [](#写入文件：w命令)写入文件：w命令

在example中所有包含test的行都被写入file里：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -n '/test/w file' example<br></pre></td>

### [](#追加（行下）：a-命令)追加（行下）：a\命令

将 this is a test line 追加到 以test 开头的行后面：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '/^test/a\this is a test line' file<br></pre></td>

在 test.conf 文件第2行之后插入 this is a test line：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -i '2a\this is a test line' test.conf<br></pre></td>

### [](#插入（行上）：i-命令)插入（行上）：i\命令

将 this is a test line 追加到以test开头的行前面：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '/^test/i\this is a test line' file<br></pre></td>

在test.conf文件第5行之前插入this is a test line：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -i '5i\this is a test line' test.conf<br></pre></td>

### [](#下一个：n命令)下一个：n命令

如果test被匹配，则移动到匹配行的下一行，替换这一行的aa，变为bb，并打印该行，然后继续：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '/test/{ n; s/aa/bb/; }' file<br></pre></td>

### [](#变形：y命令)变形：y命令

把1~10行内所有abcde转变为大写，注意，正则表达式元字符不能使用这个命令：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '1,10y/abcde/ABCDE/' file<br></pre></td>

### [](#退出：q命令)退出：q命令

打印完第10行后，退出sed
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed '10q' file<br></pre></td>

### [](#保持和获取：h命令和G命令)保持和获取：h命令和G命令

在sed处理文件的时候，每一行都被保存在一个叫模式空间的临时缓冲区中，除非行被删除或者输出被取消，否则所有被处理的行都将 打印在屏幕上。接着模式空间被清空，并存入新的一行等待处理。
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -e '/test/h' -e '$G' file<br></pre></td>

在这个例子里，匹配test的行被找到后，将存入模式空间，h命令将其复制并存入一个称为保持缓存区的特殊缓冲区内。第二条语句的意思是，当到达最后一行后，G命令取出保持缓冲区的行，然后把它放回模式空间中，且追加到现在已经存在于模式空间中的行的末尾。在这个例子中就是追加到最后一行。简单来说，任何包含test的行都被复制并追加到该文件的末尾。

### [](#保持和互换：h命令和x命令)保持和互换：h命令和x命令

互换模式空间和保持缓冲区的内容。也就是把包含test与check的行互换：
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed -e '/test/h' -e '/check/x' file<br></pre></td>

### [](#脚本scriptfile)脚本scriptfile

sed脚本是一个sed的命令清单，启动Sed时以-f选项引导脚本文件名。Sed对于脚本中输入的命令非常挑剔，在命令的末尾不能有任何空白或文本，如果在一行中有多个命令，要用分号分隔。以#开头的行为注释行，且不能跨行。
<td class="gutter"><pre>1<br></pre></td><td class="code"><pre>sed [options] -f scriptfile file(s)<br></pre></td>

### [](#打印奇数行或偶数行)打印奇数行或偶数行

方法1：
<td class="gutter"><pre>1<br>2<br></pre></td><td class="code"><pre>sed -n 'p;n' test.txt  #奇数行<br>sed -n 'n;p' test.txt  #偶数行<br></pre></td>

方法2：
<td class="gutter"><pre>1<br>2<br></pre></td><td class="code"><pre>sed -n '1~2p' test.txt  #奇数行<br>sed -n '2~2p' test.txt  #偶数行<br></pre></td>

### [](#打印匹配字符串的下一行)打印匹配字符串的下一行
<td class="gutter"><pre>1<br>2<br>3<br></pre></td><td class="code"><pre>grep -A 1 SCC URFILE<br>sed -n '/SCC/{n;p}' URFILE<br>awk '/SCC/{getline; print}' URFILE<br></pre></td>

  <li class="post-copyright-author">
    **本文作者： **atompi
  </li>
  <li class="post-copyright-link">
    **本文链接：**
    [https://blog.atompi.com/2018/01/21/每天一个Linux命令-21/](https://blog.atompi.com/2018/01/21/%E6%AF%8F%E5%A4%A9%E4%B8%80%E4%B8%AALinux%E5%91%BD%E4%BB%A4-21/)
  </li>
  <li class="post-copyright-license">
    **版权声明： **本博客所有文章除特别声明外，均采用 [<i class="fab fa-fw fa-creative-commons"></i>BY-NC-SA](https://creativecommons.org/licenses/by-nc-sa/4.0/) 许可协议。转载请注明出处！
  </li>
</script>```
