考虑这样的情形：两个头文件中中定义了相同原型的函数，而又有另外一个文件同时引用了这两个头文件。

``` c++
// aes-128.h
void handleErrors();

// MD5.h
void handleErrors();

// main.cpp
#include "aes-128.h"
#include "MD5.h"
```

此时会报错：

![image-20230302182411187](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230302182412.png)

显示重复定义了函数。

 解决方案：使用inline定义内联函数。

>inline把函数从程序级别的一次定义原则变为了翻译单元级别的一次定义原则。 

# 命名空间

如果使用了命名空间，使用命名空间区分这两个函数，那么这个重复定义的问题就可以解决。