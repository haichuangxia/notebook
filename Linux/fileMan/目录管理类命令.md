# mkdir
&emsp;&emsp;是`make directories`的缩写，其基本的作用为：` mkdir - make directories`：用于创建多个目录.其基本的格式为：`mkdir [OPTION]... DIRECTORY...`

## 基本选项
 | 选项            | 作用                                                    | 中文                                                                           |
 | --------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------ |
 | -m, --mode=MODE | set file mode (as in chmod), not a=rwx - umask          | 设置文件的权限                                                                 |
 | -p, --parents   | no error if existing, make parent directories as needed | 如果目录存在则没有错误，如果不存在父目录则可以创建父目录，可以用来创建多级目录 |
 | -v, --verbose   | print a message for each created directory              | 打印目录信息                                                                   |
 ## 常用用法
 | 用法         | 命令               |
 | ------------ | ------------------ |
 | 创建多级目录 | `mkdir -p par/son` |
 ## 注意
 1. -p选项的作用：

- 没有-p选项时：
当父目录不存在时：
    ```
    qingbin@Pc-QingBin:~/linux$ mkdir par/son
    mkdir: cannot create directory ‘par/son’: No such file or directory
    ```
- 当使用-p选项时：
    ```
    mkdir -p par/son
    #sucess
    ```

# rmdir
 &emsp;&emsp;&emsp;&emsp;其名称为：`rmdir - remove empty directories`，作用是删除<font color=red>空目录</font>，其格式为：`rmdir [OPTION]... DIRECTORY...`.命令的选项也和`mkdir`命令相似，不过多解释。

# rm
&emsp;&emsp;`rm`是一个十分强大的命令,相比于`rmdir`主要用来删除空目录,`rm`主要用来删除文件或者目录,名称来源于` rm - remove files or directories`,其基本的语法格式为:
<div align=center>

```rm [OPTION]... [FILE]...```

</div>
默认情况下,rm删除文件,而不是删除目录.

&emsp;&emsp;而文件的删除,<font color=red>实质是取消链接</font>的过程.

&emsp;&emsp;命令的几个重要参数为:
| 参数 | 意义                         |
| ---- | ---------------------------- |
| -f   | 强制删除                     |
| -i   | 交互,与-f相反,先问你是否删除 |
| -r   | 递归删除文件或者目录         |
## 删除文件
&emsp;&emsp;因为`rm`默认是删除文件,因此使用`rm`命令删除目录时就会报错.
```
$ mkdir ln
$ rm ln
rm: cannot remove 'ln': Is a directory
```
## 删除目录
&emsp;&emsp;