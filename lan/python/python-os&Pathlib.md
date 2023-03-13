# os
`os`库是python中进行系统访问的标准库,能够执行各种系统相关的操作.`pathlib`是面向对象的文件系统路径库,是`os.path`的改进版.
## 读函数
| 函数名          | 作用                   |
| --------------- | ---------------------- |
| os.path.isdir() | 指定path对象是否是目录 |

## 写函数
| 函数名        | 作用       |
| ------------- | ---------- |
| os.makedirs() | 创建文件夹 |

## 用法实例:
| 用途           | 示例                                 |
| -------------- | ------------------------------------ |
| 递归创建文件夹 | `os.makedirs(dirpath,exist_ok=True)` |
# pathlib
# datetime
datetime是一个时间日期的标准库.
`datetime.date`:与日期有关的函数,如`datetime.date.today()`
`datetime.timedelta()`:时间差有关函数

# 压缩文件类
## tarfile
一个用于创建和管理tar包的库.
### example
#### 新建tar并写入
``` python
 # there is no command like touch,so we can use this mothod to create new file
    with tarfile.open(tar_name, "w:gz") as tar:
        tar.add(source)
```

# REFERENCE
1. [pathlib官方文档](https://docs.python.org/zh-cn/3/library/pathlib.html)
2. [os.path官方文档](https://docs.python.org/zh-cn/3/library/os.path.html)