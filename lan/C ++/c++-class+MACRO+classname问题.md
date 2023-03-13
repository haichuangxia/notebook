在看Google的源代码时候,发现有`class QUIC_EXPORT_PRIVATE QuicBufferAllocator{}`和`struct QUIC_EXPORT_PRIVATE AckListenerWrapper{}`这样的写法.该宏的定义为:

``` c++
#define QUIC_EXPORT NET_EXPORT
#define QUIC_EXPORT_PRIVATE NET_EXPORT_PRIVATE
```

再次跳转,发现***NET_EXPORT_PRIVATE***和***NET_EXPORT*** 宏定义为:

``` c++
#ifndef NET_BASE_NET_EXPORT_H_
#define NET_BASE_NET_EXPORT_H_

#if defined(COMPONENT_BUILD)

    #if defined(WIN32)
        #if defined(NET_IMPLEMENTATION)
            #define NET_EXPORT __declspec(dllexport)
            #define NET_EXPORT_PRIVATE __declspec(dllexport)
        #else
            #define NET_EXPORT __declspec(dllimport)
            #define NET_EXPORT_PRIVATE __declspec(dllimport)
        #endif // defined(NET_IMPLEMENTATION)

    #else // defined(WIN32)
        #if defined(NET_IMPLEMENTATION)
            #define NET_EXPORT __attribute__((visibility("default")))
            #define NET_EXPORT_PRIVATE __attribute__((visibility("default")))
        #else
            #define NET_EXPORT
            #define NET_EXPORT_PRIVATE
        #endif

    #endif

#else /// defined(COMPONENT_BUILD)
    #define NET_EXPORT
    #define NET_EXPORT_PRIVATE
#endif

#endif // NET_BASE_NET_EXPORT_H_
```

实际上,这个用法叫做***符号导入导出***

# Windows下的符号导入导出

### h头文件 .lib库文件 .dll动态库文件之间的关系

文件类型|名称|什么时候需要|作用
-|-|-|-
.h|头文件|编译时|声明函数接口
.lib|静态链接库(Static Library)|静态链接时|直接将函数体包含到可执行文件
|dll导入库(Import Library)|动态链接时|包含被DLL导出的函数的名称和位置
.dll|动态链接库(Dynamic Library)|运行时动态装入|DLL包含实际的函数和数据

生成`.dll`动态链接库需要生成`.lib`导入库文件和`.dll`文件

## Windows下的符号导入导出
在windows下,默认的是所有符号不导出,即默认不导出`.dll`动态库文件中的符号(变量和函数等).因此,如果***A***要想调用***B***生成的`B.dll`中定义的函数`func()`需要先将`B.dll`中函数`func()`导出,然后再在***A***中导入.

实际上,不用手动定义导入导出也可以,只是性能没那么高

### 优缺点

这种导入导出方法好处呢是,提高了程序的性能,缺点呢就是比较麻烦.

### 导入导出方法

导入导出|方法
-|-
导出|在需要导出的变量或函数前加入`__declspec(dllexport)`
导入|在使用其他.dll中定义的符号的函数或结构前加入`__declspec(dllimport)`

具体的导入导出方法及其性能分析,参考:[DLL 导入和导出函数](https://learn.microsoft.com/zh-cn/cpp/c-language/dll-import-and-export-functions?view=msvc-170)

# Linux下的符号导入导出

因为Linux是默认所有符号导出,因此,不用手动进行符号导入导出.

# Windows和Linux符号导入导出对比

参考:[Linux和Windows符号导入导出的对比分析](https://blog.csdn.net/u014403008/article/details/62858230)

# 跨平台编程时的符号导入导出
为了提高跨平台的性能,因此在实际的项目开发会使用条件编译的方法来定义宏,其伪代码如下:

``` c++
if(win)
    #define IMPORT __declspec(dllimport)
    #define EXPORT __declspec(dllexport)
else
    #define IMPORT
    #define EXPORT
```

即便,在Windows平台下,其定义宏为`__declspec(dllimport)`等参数,实现导入导出.而在Linux等平台下,其定义的是一个空的宏,即`int IMPORT example`完全等于`int example`
# REFERENCE
1. [.h头文件 .lib库文件 .dll动态库文件之间的关系](https://blog.csdn.net/yusiguyuan/article/details/12649737)
1. [DLL 导入和导出函数](https://learn.microsoft.com/zh-cn/cpp/c-language/dll-import-and-export-functions?view=msvc-170)
2. [Linux和Windows符号导入导出的对比分析](https://blog.csdn.net/u014403008/article/details/62858230)