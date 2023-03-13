在工程中,常常用宏来实现一些比较高级的功能.

# 防止头文件重复包含

## 头文件重复包含

如果在一个c程序中,一个头文件被实质包含了多次,那么很可能造成变量重复定义,循环依赖等问题,而造成编译不通过.以下图为例:

``` c++
#include<stdio.h>
int va=1;
```

其中文件的依赖关系为:

``` mermaid
graph TB
B(B.h)-->|#include|A([A.h])
m([main.cpp])-->|#include|A
m-->|#include|B
```

那么在编译`main.cpp`时,因为实质包含了两次`A.h`,那么就会出现两次`int va=1`,编译会失败.

## 使用条件编译防止头文件重复包含

通过定义无值的宏可以防止头文件重复包含.使用了条件编译的头文件`example.h`基本格式为:

``` c++
#ifndef MACRO
#define MACRO

... ...
    

#endif
```

当第一次包含`example.h`,此时并没有定义`MACRO`宏,因此会继续执行,而当第二次包含`example.h`时,由于第一次包含`example.h`时已经定义`MACRO`宏,因此程序直接跳转到`#endif`,这样就避免了一个头文件多次包含.

# 跨平台符号导入导出
由于Linux和Windows平台的符号导入导出策略不同,因此可以使用条件编译,在不同平台下定义不同的宏,以此实现代码的跨平台.

## 定义宏
``` c++
if(win)
    #define IMPORT __declspec(dllimport)
    #define EXPORT __declspec(dllexport)
else
    #define IMPORT
    #define EXPORT
```

## 使用宏
在A.h中定义需要导出的变量
`int EXPORT a=1`
在B.h中定义需要使用变量a的符号
``` c
void IMPORT print(){
	printf("the value of the variable imported from A.h is: %d\n",a)
}
```

具体用法参见:https://juejin.cn/post/7155856282082082823



# REFERENCE

1. [C++工程中常用的宏定义(#define)](https://cloud.tencent.com/developer/article/1058023)