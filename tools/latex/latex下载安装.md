## 1. 关于latex的整体流程

  使用latex，有几个流程，其为：

``` mermaid
flowchart LR
write[编写tex文件]-->compile[编译]
```

对于编写tex文件，其基本的组成部分为：

对于tex的编译，有两个常见的命令，pdflatex和xelatex，两个命令的用途为：

- pdflatex：一般用于编译英文环境下的论文
- xelatex：一般用于编译中文环境下的论文
## 2. latexindent进行格式化
对latex文档进行格式化需要使用latexindent，这是一般perl语言编写的脚本。如果没有安装，则需要安装之后才能格式化。
### 1. latexindent的安装和配置
#### 1. Windows下latexindent的安装
##### 1. 下载latexindent压缩包并解压到targetpath
[latexindent上交源](https://mirrors.sjtug.sjtu.edu.cn/ctan/support/latexindent/)

[latexindent爱荷华州立大学源](https://mirror.las.iastate.edu/tex-archive/support/latexindent.zip)
##### 2. 将targetpath/latexindent加入环境变量
##### 3. 使用latexindent进行文档格式化
##### 4. latexindent安装时缺少perl库问题的解决方法
&emsp;&emsp;在安装latexindent时,有可能会遇到缺少部分库的问题,如提示缺少Log::Log4perl库,报错如下:
```
Can't locate Log/Log4perl.pm in @INC (you may need to install the Log::Log4perl module)
```

&emsp;&emsp;此时需要自己去安装这些库,自己去安装这些库的时候有可能失败.我自己的解决方法是使用管理员权限手动的安装这些库,也试过其他方法,但是没有奏效.使用cpan工具手动安装perl模块,参考REFERENCE-2.
#### 2. Linux下latexindent的安装
## REFERENCE
1. [解决latexindent无法运行的问题](https://zhuanlan.zhihu.com/p/50044410)
2. [linux perl cpan 安装使用](https://blog.csdn.net/whatday/article/details/122251716)