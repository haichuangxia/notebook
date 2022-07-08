1. 命令摘要:
   1. cat:连接文件并打印
   2. more:分页显示文本文件的内容.
   3. less:more命令的加强版
   4. head:显示文件前若干行的内容
   5. tail:显示文件后若干行的内容

## cat
`cat`的功能是**连接多个文件并将其打印出来**,使用`man cat`命令查询该命令的帮助文档,可以发现其文档说明为:

```
NAME
       cat - concatenate files and print on the standard output

SYNOPSIS
       cat [OPTION]... [FILE]...
```

因此该命令实现的主要操作为:
1. 连接多个文件
2. 在控制台打印连接后的文件内容

### OPTION
| 项     | 长格式             | NAME   | 含义                                                     |
| ------ | ------------------ | ------ | -------------------------------------------------------- |
| -A     | --show-all         | All    | 相当于 -vET 选项的整合，用于列出所有隐藏符号；           |
| -E     | --show-ends        | end    | 列出每行结尾的回车符`$`；                                |
| **-n** | --number           | number | 对输出的所有行进行编号；                                 |
| **-b** | --number-nonblank  | blank  | 与-n 相似但不同，此选项表示只对非空行进行编号。          |
| -T     | --show-tabs        | TAB    | 把 Tab 键 以`^I`显示出来；                               |
| -V     | --show-nonprinting |        | 列出特殊字符；                                           |
| -s     | --squeeze-blank    |        | 当遇到有连续 2 行以上的空白行时，就替换为 1 行的空白行。 |
### 常见用法
约定有两个文件,hello.txt和world.txt,hello.txt中只有一个单词hello,world.txt中只有一个单词world.即使用cat命令输出两个文件的内容,其结果为:
```
$ cat hello.txt
hello
$ cat world.txt
world
```
#### 打印文件内容
使用`cat [option] [File]...`格式命令,可以用来打印这多个文件的内容.例如有`hello.txt`和`world.txt`两个文件,使用cat显示两个内容的示例命令为:
```
$ cat -n hello.txt world.txt
     1  hello
     2  world
```

#### 连接,打印并保存文件
默认情况下,多个文件连接后并不会保存,也就是说,默认情况下,使用`cat`命令只是单纯的打印文件的内容,并不会保存多个文件连接后的结果.因此要将连接后的结果保存到一个文件,可以使用**`>`重定向输出**符号将连接后的内容保存到另一个文件中.

注意:
在使用重定向输出符保存连接后的文件内容时,`cat -n hello.txt world.txt > hello_world.txt`与`cat hello.txt world.txt > hello_world.txt`的结果并不相同.
<table>
<tr>
<td>无<B>-n</B>参数</td>
<td>有<B>-n参数</B></td>
</tr>
<tr>
<td>
<pre>
$ cat hello.txt world.txt > hello_world.txt
$ cat hello_world.txt
hello
world
</pre>
</td>
<td>
<pre>
$ cat -n hello.txt world.txt > hello_world.txt
$ cat hello_world.txt
     1  hello
     2  world
</pre>
</td>
</tr>
</table>

可以发现,使用`>`重定向输入符将连接后的文件写入新文件时,并不会简单的合并两个文件,而是保存打印输出的内容.


## more
`more`命令用于以**分页**的方式,详细的查看文件的内容,尤其适合适合那些有大量文字的文件内容显示.

命令的基本格式为:` more [options] file...`

### options


表 1 more 命令选项及含义
| 选项 | NAME         | 含义                                                     |
| ---- | ------------ | -------------------------------------------------------- |
| -f   |              | 计算行数时，以实际的行数，而不是自动换行过后的行数。     |
| -p   |              | 不以卷动的方式显示每一页，而是先清除屏幕后再显示内容。   |
| -c   | clear        | 跟 -p 选项相似，不同的是先显示内容再清除其他旧资料。     |
| -s   | squeeze,压缩 | 当遇到有连续两行以上的空白行时，就替换为一行的空白行。   |
| -u   | underline    | 不显示下引号（根据环境变量 TERM 指定的终端而有所不同）。 |
| +    |              | 后接阿拉伯数字作为参数,从第 n 行开始显示文件内容         |
| -    |              | 后面接阿拉伯数字作为参数,一次显示的行数                  |

### vi基本控制命令

`more`命令交互式显示文本内容是基于`vi`的,因此可以用`vi`的控制命令实现交互式显示文本的内容.`vi`编辑器的几个命令为:

| 交互指令            | NAME      | 功能                                 |
| ------------------- | --------- | ------------------------------------ |
| h 或 ？             | help      | 显示 more 命令交互命令帮助。         |
| q 或 Q              | quit      | 退出 more。                          |
| v                   | vi        | 在当前行启动`vi`编辑器编辑文本的内容 |
| :f                  | file      | 显示当前文件的文件名和行号。         |
| !<命令> 或 :!<命令> |           | 在子Shell中执行指定命令。            |
| 回车键              |           | 向下移动一行。                       |
| 空格键              |           | 向下移动一页。                       |
| d                   | down      | 向下移动半页。                       |
| b                   | backwards | 向上移动一页。                       |
| Ctrl+l              |           | 刷新屏幕。                           |
| =                   |           | 显示当前行的行号。                   |
| '                   |           | 转到上一次搜索开始的地方。           |
| Ctrf+f              | follw     | 向下滚动一页。                       |
| .                   |           | 重复上次输入的命令。                 |
| / PATTEN            |           | 搜索指定的字符串。                   |

不过这些命令并不需要记,当显示之后,按`h`获取帮助就行,只要英语水平不是太差,是可以看懂的.
### 示例用法
<table>
<tr><td>用法</td><td>示例</td></tr>
<tr>
<td>指定显示5行</td>
<td>
<pre>

$ more -5 paper.tex
\documentclass[12pt, a4paper, oneside]{ctexart}
\usepackage{amsmath, amsthm, amssymb, graphicx}
\usepackage{multirow}
\usepackage[T1]{fontenc}
\usepackage[table,xcdraw]{xcolor}

</pre>
</td>
</tr>

<tr>
<td>从第3行开始显示</td>
<td>
<pre>

$ more +3 -2 paper.tex
\usepackage{multirow}
\usepackage[T1]{fontenc}

</pre>
</td>
</tr>

</table>

## less
`less`命令与`more`命令十分类似,不过其功能更强大,不仅支持向后查看,也支持向前查看.该命令是基于`more`和`vi`的.其`NAME`为` less - opposite of more`.同时,它在启动前并不一次性读入文件内容,因此尤其适合大文件的读取.
### option
`less`命令的选项和`more`命令的类似,如果要查看,使用`man less`查看就可.
### 交互式命令
同样,使用`more`命令打印文件内容之后,可以使用`h`命令获取交互式命令.其有别于`more`命令的基本交互项为:

约定:
`^`符号表示`CTRL`键或者`command`键.`NUMBER`表示阿拉伯数字,`PATTERN`表示支持正则和通配符的字符串.

| 命令类别 | 命令            | 默认参数 | 功能                   | Example             |
| -------- | --------------- | -------- | ---------------------- | ------------------- |
| 移动光标 | e ^E等 [NUMBER] | 1        | 向**前**移动NUMBER行   | e+8:表示向前查看8行 |
|          | y ^Y等 [NUMBER] | 1        | 向**后**移动NUMBER行   | e+8:表示向后查看8行 |
|          | G               |          | 移动到**最后一行**     |                     |
|          | g               |          | 移动到**第一行**       |                     |
| 搜索     | / PATTERN       |          | 向**下**搜索指定的模式 |                     |
|          | ? PATTERN       |          | 向**上**搜索指定的模式 |                     |
## head
`head`命令用来显示文件们的前面一部分,其帮助文档是这么说的:`head - output the first part of files`.

命令的基本格式为` head [OPTION]... [FILE]...`
### option
| 短格式 | 长格式              | 含义                                                            |
| ------ | ------------------- | --------------------------------------------------------------- |
| `-c`   | `--bytes=[-]NUM`    | 后接阿拉伯数字`number`作为参数,表示打印文件前`number`个**字节** |
| `-n`   | ` --lines=[-]NUM`   | 后接阿拉伯数字`number`作为参数,表示打印文件前`number`**行**     |
| `-q`   | `--quiet, --silent` | 只打印文件的内容,**不打印文件的标题**                           |
| `-v`   | `--verbose`         | 打印文件的内容,且**要打印文件的标题**                           |
### 常见用法
#### 打印前K个字节
```
$ head -c 100 paper.tex
\documentclass[12pt, a4paper, oneside]{ctexart}
\usepackage{amsmath, amsthm, amssymb, graphicx}
```
#### 打印前K行
```
$ head -n 2 paper.tex
\documentclass[12pt, a4paper, oneside]{ctexart}
\usepackage{amsmath, amsthm, amssymb, graphicx}
```

#### 打印文件时也打印文件标题

```
$ head -v -n 2 paper.tex
==> paper.tex <==
\documentclass[12pt, a4paper, oneside]{ctexart}
\usepackage{amsmath, amsthm, amssymb, graphicx}
```
使用`-v`选项设置打印文件的名称,使用`-n 2`指定打印文件的前两行.
#### 打印文件时不打印文件标题[default]
```
$ head -q -n 2 paper.tex
\documentclass[12pt, a4paper, oneside]{ctexart}
\usepackage{amsmath, amsthm, amssymb, graphicx}
```

相比于上面,这个选项不会打印文件的名称,这也是默认的设置,默认不打印文件名称.
## tail
与`head`命令相反,查看文档末尾的内容,参数和选项与`head`类似.其不同之处在于其可以使用`-f`选项显示文件变化后新增的内容.