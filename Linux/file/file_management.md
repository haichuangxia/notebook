1. 命令摘要:
   1. ls
   2. tree
   3. mv
2. 涉及到的问题
   1. 查询文件信息
   2. 生成目录树
   3. 文件的移动和重命名
# 查看文件基本信息
## `file`命令

使用`file`命令可以查看一个文件的格式,如`file main.o`

## `ls`命令
&emsp;&emsp;文件的详细信息可以通过`ls`命令来查看，`ls`是`list`的缩写。使用`ls -l`命令来详细查看文件的信息，默认是目录下所有的文件。其基本的命令格式为：`ls [option]... [file]...`,其中`...`表示可以有多个选项或者参数。
### 参数的基本含义
| 参数 | 意义                                                                                  |
| ---- | ------------------------------------------------------------------------------------- |
| -R   | 连同子目录内容一起列出来，等于将该目录下的所有文件都显示出来。                        |
| -S   | 以文件容量大小排序，而不是以文件名排序。                                              |
| -h   | 以人们易读的方式显示文件或目录大小，如 1KB、234MB、2GB 等。                           |
| -i   | 显示 inode 节点信息。                                                                 |
| -l   | 使用长格式列出文件和目录信息。                                                        |
| a    | 显示全部的文件，包括隐藏文件（开头为 . 的文件）也一起罗列出来，这是最常用的选项之一。 |
| -A   | 显示全部的文件，连同隐藏文件，但不包括 . 与 .. 这两个目录。                           |

### 文件基本信息的解读
使用`ls -l`以长格式显示文件的基本信息，其每列的基本含义为：
| 1                    | 2                      | 3        | 4       | 5                    | 6            | 7      |
| -------------------- | ---------------------- | -------- | ------- | -------------------- | ------------ | ------ |
| 不同用户对文件的权限 | 引用计数，硬链接的个数 | 所有用户 | 所有组  | 大小，默认单位是字节 | 文件修改时间 | 文件名 |
| drwxr-xr-x           | 1                      | qingbin  | qingbin | 512                  | May 22 17:21 | git    |


命令行下，其命令结果为：
```
qingbin@Pc-QingBin:~$ ls -l
total 0
drwxr-xr-x 1 qingbin qingbin 512 May 22 17:21 git
-rwxrwxrwx 1 qingbin qingbin  45 Jul  3  2021 init.sh
drwxr-xr-x 1 qingbin qingbin 512 May 27 01:37 latex
drwxr-xr-x 1 qingbin qingbin 512 Jun  1 13:06 linux
```

### ls的几个常见的用法：
| 用法                                 | 命令                    |
| ------------------------------------ | ----------------------- |
| 显示特定目录详细信息，而不是其子目录 | `ls -ld <directory>...` |

## 生成目录树
&emsp;&emsp;windows和Linux下都可以通过`tree`命令来生成文件树。生成文件之后，还可以通过`>`输出重定向符将打印的文件树保存到文件中。

命令的基本格式为：
```
tree
[-acdfghilnpqrstuvxACDFQNSUX]
[-L  level  [-R]]  [-H  baseHREF]
[-T title] [-o filename] [--nolinks]
[-P pattern] [-I pattern] [--inodes]
[--device] [--noreport] [--dirsfirst]
[--version] [--help] [--filelimit #]
[--si] [--prune] [--du] [--timefmt format]
[--matchdirs] [--fromfile] [--]
[directory ...]
```

### 常见的参数意义
| 参数      | 名称      | 意义                         |
| --------- | --------- | ---------------------------- |
| -a        | all       | 显示全部文件                 |
| -d        | directory | 只显示文件夹，不显示文件     |
| -L number | level     | 限制显示等级                 |
| -c        | color     | 以系统默认色彩方案显示文件树 |

### 用法
对于下列文件树，可以使用`tree -L 2 . > tree.txt`命令将输出的文件树保存到文本文件中。
```
├── d1
│   ├── dd1
│   └── dd2
├── d2
└── d3
```


# 文件移动和重命名

1. 问题描述：

使用`mv`命令可以对文件进行重命名，不过在对带有空格的文件进行重命名时就会产生一定的问题。例如有一个文件`hello world.txt`,需要对其进行重命名，改成`hello.txt`,如果只是使用`mv <file> <newfilename>`,就会产生问题，其问题如下：
```
$ mv hello world.txt hello.txt
# mv: target 'hello.txt' is not a directory
```
2. 问题分析：

Linux中，命令的不同成分使用**空格**作切分的依据，因此在处理带有空格的文件时，`mv`指令就会将其看作是两个文件，误认为是`mv [op]... SOURCE... DIRECTORY`用法，用于将多个SOURCE文件，移动到DIRECTORY目录下。

3. 解决方法

其主要的解决方法有两个：
- 使用转义符进行转义

`mv hello\ world.txt hello.txt`

- 使用双引号

`mv "hello world.txt" hello.txt`