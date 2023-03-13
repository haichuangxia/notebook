`diff`和`patch`是Linux下进行文件改动跟踪的工具，是git等版本控制工具的依赖项。

`diff`和`patch`可以看作集合操作，`diff`寻找差集，`patch`可以实现$new=old+patch$和$old=new=patch$的操作

# diff
`diff`命令主要是以**逐行对比**的方式，比较两个文本文件之间的差异。命令的基本格式为`  diff [OPTION]... FILES
`

常用的语法格式为：`diff -urN <old file> <new file>`
## 常用参数
| 参数 | long      | 意义                                         |
| ---- | --------- | -------------------------------------------- |
| -u   | unified   | 使用unified模式进行输出，比较实用的输出方式  |
| -r   | recursive | 递归的比较                                   |
| -N   | new file  | 将命令中存在，而实际缺失的文件当作空文件处理 |

## unified模式下输出结果解读
对于两个文本文件，**test1.txt**和**test2.txt**，其内容分别为：

- test1.txt
```
linux \n linux
linux
linux
```

- test2.txt

```
locez
linux
locez
linux
```

使用`diff`命令查看两个文件之间的差异：`diff -urN test1.txt test2.txt`,输出结果为：
```
--- test1.txt	2022-09-11 09:43:09.542087149 +0800 # 表示改变前的文件
+++ test2.txt	2022-09-11 09:43:34.262097471 +0800 # 表示改变后的文件
@@ -1,4 +1,4 @@ # 上下文描述块
+locez # 新增
 linux
-linux # 删除
-linux
+locez
 linux
```

## 创建补丁文件
补丁文件后缀名为`.patch`,可以使用标准的输出重定向生成补丁文件，如`diff -urN test1.txt test2.txt > diff.patch`。

# patch
使用`diff`创建了补丁文件之后,就可以使用`patch`命令应用补丁文件来更新文件。最简单的命令为：`patch <origin> <patch>`

## 重要参数
| 参数 | long    | 意义                                             |
| ---- | ------- | ------------------------------------------------ |
| -o   | outfile | 将使用补丁进行更新之后的内容保存到一个新的文件中 |
| -R   | reverse | 可以要把文件恢复成更新之前的操作                 |

## 常见用法
### 1. 应用补丁进行更新
`patch <origin> patch`,实现$new=old+patch$

### 2. 撤销补丁更新呢
`patch -R <origin> patch`,实现$old=new-patch$

# REFERENCE
1. [diff和patch使用指南](https://blog.51cto.com/u_15462545/4878091)
1. [使用 Linux 的 diff 和 patch 对文件进行协作 ](https://linux.cn/article-14037-1.html)
1. [diff命令和patch命令的使用](https://www.jianshu.com/p/6ee74c2790b1)
2. [diff 与 patch 的使用](https://zhuanlan.zhihu.com/p/37635124)