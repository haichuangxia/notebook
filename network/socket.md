# socket介绍

`socket`是对`TCP`和`UDP`等协议栈进行操作的接口,所以`socket`编程又可以分为`TCP编程`和`UDP`编程.

# 基础

## 网络字节序与地址变换

不同CPU中数据的存储方式不同，有大端存储与小端存储。以int i=1为例：

| 存储方式 | 特点             | 1的存储形式（从低地址到高地址）     |
| -------- | ---------------- | ----------------------------------- |
| 大端存储 | 高位存储在低地址 | 00000000 00000000 00000000 00000001 |
| 小端存储 | 高位存储在高地址 | 00000001 00000000 00000000 00000000 |

在计算机中,CPU对数据的保存方式叫做:**主机字节序(Host Byte Order)**.目前主流的**intel**处理器以**小端序**方式保存数据.

与计算机CPU保存数据的**主机字节序**对应,通过网络传输数据时的字节顺序叫做:**网络字节序(Network Byte Order)**.网络字节序统一为:**大端序**.


因此，对不同的数据存储方式，应该选用相应的数据解析方式，否则会出现错误。在网络传输方面,需要将主机字节序与网络字节序进行转换.

# TCP编程

| 过程   | 作用                                                |
| ------ | --------------------------------------------------- |
| init   | server初始化`socket`,得到文件描述符                 |
| bind   | 绑定IP地址和端口                                    |
| listen | 监听端口(套接字)                                    |
| accept | 等待客户端连接,收到连接请求后返回一个新的文件描述符 |
| read   | 服务器使用read读取数据                              |
| close  | 关闭连接                                            |

也就是服务器需要创建两个socket,在listen阶段创建一个用于监听,在accept阶段,收到client的请求连接后,创建一个socket用于网络通信.