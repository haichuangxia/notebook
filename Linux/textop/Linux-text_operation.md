命令摘要:
1. grep
2. sed
3. awk

# grep
&emsp;&emsp;命令介绍为:`print lines matching a pattern`,命令的全称为:`global regular expressions print`,该命令使用正则表达式搜索文件中模式匹配的内容,一旦找到则输出.



其用法签名为：

![image-20221210224151565](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221210224152.png)

## 默认参数

该命令大小写表示相反的意思

| 参数名      | 作用                                             |
| ----------- | ------------------------------------------------ |
| -l          | 仅仅列出包含该字符串的文件路径以及名称           |
| -H[default] | 在查找到的对文件对应行前面显示该行所属文件的名称 |
| -n          | 在查找的对应行前显示其对应的行号                 |
| -E          | 将对应的查找语句当成正则表达式来查找             |
| -r          | 递归查找文件以及目录                             |



## EXAMPLE

| 命令             | 用法                                         |
| ---------------- | -------------------------------------------- |
| grep -r hello .  | 递归的在当前目录下查找包含“hello”的行        |
| grep -rl hello . | 递归的查找行，仅仅列出含有查找对象的文件路径 |
|                  |                                              |



# sed

# awk
# tr
`tr`命令用于从标准输入设备读取数据,经过字符串处理之后,将结果输出到标准输出设备,主要用途为**转换**或**删除**文件中的字符.

其语法为:
| 命令 | NAME                           | 语法                   |
| ---- | ------------------------------ | ---------------------- |
| tr   | translate or delete characters | tr [OP]... SET1 [SET2] |

常用参数为:
| 参数 | NAME     | 含义         |
| ---- | -------- |
| -d   | --delete | 删除指定字符 |

## 用法
替换指定字符
`cat testfile| tr a-z A-Z`:将小写字符转换成大写字母