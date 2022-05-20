## 1. 关于latex的整体流程

  使用latex，有几个流程，其为：

``` mermaid
flowchart LR
write[编写tex文件]-->compile[编译]
```

对于编写tex文件，其基本的组成部分为：

对于tex的编译，有两个常见的命令，pdflatex和xelatex，两个命令的用途为：

- pdflatex：一般用于编译英文环境下的论文等
- xelatex：一般用于编译中文环境下的论文
