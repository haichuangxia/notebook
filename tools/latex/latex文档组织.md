&emsp;&emsp;latex以**document**环境为界，前面是**导言区**，主要进行格式设置，**document**环境中是**正文**，document之后的内容被忽略。
1. 导言区
   1.  标题
       1.  title
       2.  author
       3.  data
       4.  maketitle
   2.  摘要/前言
       1.  abstract环境
   3.  目录
       1.  tableofcontents
   4.  章节
       1.  chapter
       2.  section
   5.  附录
       1.  appendix
   6.  文献
       1.  \bibligraphy
   7.  索引
       1.  printindex
2.  文档划分
    1.  大型文档划分
    2.  一般文档划分
3.  磁盘文件组织
&emsp;&emsp;对于比较大型的文档，可以将一个文档分成多个文件，并且划分文件的目录结构
- 主文档，给出文档框架结构
- 按照章节内容划分不同文件
- 使用单独的文件设置格式（类文件或者格式文件）
- 用小文件隔离复杂图表
&emsp;&emsp;相关的命令
- \documentclass:读入文档类（.cls）
- \usepackage:读入格式文件--一个宏包（.sty）
- \include:分页，读入章节文件
- \input:读入任意文件