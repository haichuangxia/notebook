## 打包(归档)与压缩的概念区别
### 归档/打包
归档之后产生一个`archive`,**是文件或者目录的集合,这个集合以一个文件的形式进行保存**.归档后产生的文件,并不会进行压缩,所以其存储空间是集合内各个元素的大小之和.

通常,归档总是和数据备份联系.
### 压缩
类似于归档,但是压缩会使用一定的方法减少文件中的数据冗余,因此压缩包的大小小于各个元素大小之和.

## 常见的归档和压缩包格式介绍
Linux中,最常见的archive格式为`.tar`,压缩包则有`.gz`和`.zip`等,涉及到的命令有:`tar`,`gzip`和`zip`等.
`.tar.gz`文件是先通过`tar`进行归档之后再通过`gzip`来压缩.

``` mermaid
flowchart LR
A([*.*])--tar-->B(*.tar)
B--gzip-->gz([.tar.gz])
A--tar -z-->gz

```

## 常用的归档和压缩命令介绍
### tar
`tar`是最常用的归档(打包)命令,其可以将多个文件保存到一个单独的磁盘中进行归档.使用`tar`命令归档的包通常称为`tar`包,后缀名为`.tar`

其基本的使用格式为:`tar [Operation mode][ARG...]`,`[operation mode]`指定命令的不同功能.tar命令可以用于打包,解包等功能,具体使用的是哪个功能,使用`operation mode`来指定.
#### Operation mode
使用不同的模式,决定了命令的不同功能.**operation mode是必选项**,不可漏掉.
| 短格式 | 长格式                   | 说明（助记词）                                      | 说明（用法）                                                     |
| ------ | ------------------------ | --------------------------------------------------- | ---------------------------------------------------------------- |
| -A     | --catenate,--concatenate | Append，附加                                        | 将新的文档附加到已存在的文档中。新文档与已存在文档必须同类型。   |
| -c     | --create                 | create，创建                                        | 建立新的文档                                                     |
| -d     | --diff,--compare         | differences，差异（find differences <=>compare比较) | 比较档案文件与文件系统                                           |
| 无     | --delete                 | delete(删除）                                       | 删除档案文件中的部分内容                                         |
| -r     | --append                 | append，附加（r与a音近）                            | 将文件归档，并附加到已存在的档案文件                             |
| -t     | --list                   | list，列出                                          | 列出档案文件中的内容                                             |
| u      | --update                 | update，更新                                        | 档案文件中某部分内容存在新版本，将该部分的新版本附加到档案文件中 |
| x      | --extract,--get          | extract，提取                                       | 从档案文件中提取内容                                             |
| -f     | --file                   | archive name                                        | -f参数后面必须跟archive的名字,如tar -czf hello.tar.gz /dir       |
| -z     | --gzip, --gunzip,ungzip  | gzip                                                | 调用gzip程序进行压缩解压缩                                       |
#### tar用于打包
##### 常用选项及其含义

| 选项 | 全称    | 含义                                                                                     |
| ---- | ------- | ---------------------------------------------------------------------------------------- |
| -c   | create  | 将多个文件或目录进行打包。                                                               |
| -A   | append  | 追加 tar 文件到归档文件。                                                                |
| -f   | file    | 包名	指定archive的文件名。包的扩展名是用来给管理员识别格式的，所以一定要正确指定扩展名； |
| -v   | verbose | 显示打包文件过程；                                                                       |

##### 常见用法
###### 打包文件
 `tar -c [-f ARCHIVE] [OPTIONS] [FILE...]`格式的命令实现将多个文件打包成一个archive,`-c`参数表示新建archive,`-f`表示写入的archive文件名,`[FILE...]`选项表示要归档的那些文件,可以是多个文件,也可以是目录.

使用示例:
`tar -cvf ~/hello.tar /hello`该命令将`/hello`文件夹下的所有文件归档到`~`目录,archive的名称叫做`hello.tar`.`-cvf`表示创建archive文件并显示归档过程.

注意:

**archive的目录必须放到`-f`参数后面,参数组合是有顺序的,因此`-cf`参数是合法的,但是`-fc ~/hello.tar`是非法的.**
###### 追加到archive
` tar -A [OPTIONS] ARCHIVE ARCHIVE`格式命令,可以将一个archive追加到另一个archive.

如使用`tar -Af hello.tar world.tar`命令将world.tar追加到hello.tar.

#### tar用于解打包
tar命令中,`-x`operation mode表示使用解打包功能.解打包的基本格式为:` tar -x [-f ARCHIVE] [OPTIONS] [MEMBER...] `.`[MEMBER]`可以表示其他选项,比如解压路径.
##### 常见选项的含义
| 选项 | 含义                                                       |
| ---- | ---------------------------------------------------------- |
| -x   | 对 tar 包做解打包操作。                                    |
| -f   | 指定要解压的 tar 包的包名。                                |
| -t   | 只查看 tar 包中有哪些文件或目录，不对 tar 包做解打包操作。 |
| -C   | 目录	指定解打包位置。                                      |
| -v   | 显示解打包的具体过程。                                     |

##### 常见用法
###### 基础用法
`tar -xvf hello.tar`格式的命令,用于将一个archive进行解档,默认是解档到当前目录下,创建同名的目录.
###### 指定解压位置
使用`-C`命令指定解档的位置,其基本格式为:`tar -xf example.tar  -C PATH`.例如:`tar -xvf hello.tar -C /hello`.
#### 压缩
tar命令主要同于归档,并不实现对archive的压缩.对于已经进行归档的archive文件,需要使用**gzip**进行压缩,压缩后的文件后缀名为`.tar.gz`

`tar`命令对文件进行归档并压缩,需要使用`-z`参数进行压缩.

Example:`tar -zcf hello.tar /hello`

#### 解压
##### 不同压缩格式解压参数
不同的压缩文件需要使用不同的参数进行解压,压缩格式与参数的对应关系为:
| 压缩文件格式 | 参数 |
| ------------ | ---- |
| *.tar.gz     | -z   |
| *.tar.xz     | -J   |
| *tar.bz2     | -j   |
### gzip
一个压缩文件的命令,压缩包的后缀名为`.gz`
### unzip
`unzip`命令的基本格式为:`unzip [-Z] [-cflptTuvz[abjnoqsCDKLMUVWX$/:^]] file[.zip] [file(s) ...]  [-x xfile(s) ...] [-d exdir]`,命令的基本参数为:
| 参数                               | 含义                                                                       |
| ---------------------------------- | -------------------------------------------------------------------------- |
| [-Z]                               |                                                                            |
| [-cflptTuvz[abjnoqsCDKLMUVWX$/:^]] | 表示可选项,这里用的是正则化的方式定义的选项                                |
| file[.zip]                         | **必选**要解压的压缩包路径,**支持通配符**                                  |
| [file(s)...]                       | **可选** 要处理的压缩包内的元素(member),用来设置压缩包内哪些文件可以被处理 |
| [-x xfile(s)]                      | **可选**,决定压缩包内哪些文件需要解压                                      |
| [-d exdir]                         | **可选**,解压到已经存在的目录                                              |

#### 选项及其含义
## CONCLUDE
1. 归档不改变archive的大小,压缩则会减少压缩包的大小
2. `tar -cf [archive] [FILE...]`创建.tar文件
3. `tar -czf [archive] [FILES...]`创建tar.xz文件
4. `tar -xf [archive]`解档到当前目录
5. `tar -xf [archive] -C [target]`解档到指定目录
6. `tar -zxf [tar.gz]`对tar.gz文件进行解压缩
## REFERENCE
1. [Linux命令行批量解压](https://blog.csdn.net/weixin_42426841/article/details/124087499)
2. [linux tar 基本格式、常用选项、压缩与解压缩](https://blog.csdn.net/xiaohaier8593/article/details/112424512)