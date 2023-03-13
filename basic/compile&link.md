# 程序执行的过程
程序执行的一般过程为:![程序执行过程](../picture/compile%26link.png)
# compile
编译(compile):通过编译器将文本的**高级语言源程序**转换成**机器语言**或者**汇编语言**.多少个源程序,如`.c`就需要进行多少次编译.

编译实质就是将文本文件变成二进制文件.有些编译器完成了编译和汇编两个工作,统称为编译

在C程序中`#include <stdio.h>`头文件就在**编译**阶段被使用,并和源代码一起编译,实质就是将`stdio.h`中定义的函数替换到`#include`的位置,是一种宏的替换.

# 汇编
将**汇编语言**转换为**机器语言**的过程叫做**汇编**


# link
对于经过编译和汇编之后的机器语言代码还不能立即执行,因为它只是将高级语言翻译成了机器语言,要想真正运行,还需要经过**link**操作,将程序依赖的一些标准库和动态库一并进行打包.

由于`.h`头文件只定义了函数的声明而没有定义函数的实现,因此在**link**阶段,链接器会将具体的函数实现引入,如`stdio.h`头文件的实现一般都是在一些标准库中(`.lib`文件)

在链接阶段,会将这些库(`.lib`或者`.o`)与编译好的机器语言文件放到一起.