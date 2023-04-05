# windows和Linux下的socket

在Windows下,socket相关函数主要在`winsock2.h`

# UDP编程

udp是无连接的通信协议,因此不用调用TCP连接过程中的`listen()`函数和`accept`函数.对于UDP协议来说,主要完成**创建套接字**和**数据交换过程**.

``` mermaid
flowchart TB
subgraph server
  s([socket])-->sb[bind]
  sb-->sr[recvfrom]-->ss[sendto]-->sc([close])
  end

subgraph client
  c([socket])-->cs[sendto]-->cr[recvfrom]-->cc([close])
  end

cs-->|request|sr
ss-->|response|cr
```

## 创建套接字

### Linux下的socket

#### 定义

``` c++
#include <sys/socket.h>
/**
domain(protocol family) 套接字中使用的协议簇信息,ipv4,v6等
type 套接字数据传输类型信息,连接还是无连接
protocol 计算机通信中使用的协议信息
*/

int socket(int domian,int type,int protocol)
```

#### 参数说明

| 参数     | 选项        | 意义                                 |
| -------- | ----------- | ------------------------------------ |
| domain   | PE_INET     | IPv4协议簇                           |
|          | PE_INET6    | IPv6协议簇                           |
| type     | SOCK_STREAM | 创建面向连接的套接字(不存在数据边界) |
|          | SOCK_DGRAM  | 创建面向消息的套接字                 |
| protocol | IPPROTO_UDP | UDP协议,协议号为17                   |
|          | IPPROTO_TCP | TCP协议,协议号8                      |
|          | 0           | 使用缺省的协议                       |

创建UDP,TCP socket的套接字:
`int udp_socket = socket(PF_INET,SOCK_DGRAM,IPPROTO_UDP)`

创建TCP socket:
`int tcp_socket = socket(PF_INET,SOCK_STREAM, IPPROT_TCP);`

### Windows下的socket

在`winsock2.h`中,`socket()`函数的定义为:`SOCKET socket(int af, int type, int protocol);`

返回值`SOCKET`为一个整形的结构体变量.Linux下创建套接字创建失败返回`-1`,但是Windows下,`SOCKET`的值不一定为`-1`.

## 绑定端口

### bind()

``` c++
#include <sys/types.h>
#include <sys/socket.h>

/**
sockfd socket()返回的文件描述符
addr 指向数据结构struct sockaddr的指针
addrlen 地址结构体(struct sockaddr)的长度
int bind(int sockfd, struct sockaddr* addr, socklen_t addrlen);
```

`bind()`函数是**服务器**调用的函数,用于将服务器进程和ip:port套接字进行绑定.因为一个服务器上可能有多个网卡,所以服务器除了要绑定**端口**之外,还需要绑定网卡(IP地址).

如果要监听服务器上所有网卡(ip)的指定端口,则可以在`bind()`函数中将ip设置为:`INADDR_ANY`,表示只要是发送到服务器的`PORT`端口的,无论是哪个网卡/IP收到的,都由该服务端进行处理.

bind()的过程为:

``` mermaid
graph TB
subgraph sockaddr
port_string([字符型port])-->|atoi函数|port_int
  port_int([整数port])-->|htons函数|sin_port-->addr_in[sockaddr_in]
  ip([IPv4])-->|inet_addr|In_addr_t[s_addr]-->|in_addr.s_addr=s_addr|sin_addr[struct in_addr]-->addr_in
  ip-->|inet_aton|sin_addr
  AF_Inet-->addr_in
  end
  addr_in-->|const struct sockaddr*强制类型转换|addr[sockaddr]
  addr-->bind
  addr-->|sizeof函数|bind
  sock-->bind
```

### sockaddr结构体
#### sockaddr_in结构体
`sockaddr_in`中,`in`表示`Internet`

创建套接字之后,需要绑定地址和端口,此时需要使用`struct sockaddr_in`结构体将地址信息传送给`bind()`.`sockaddr_in`的结构体声明为:

在`struct sockaddr_in`结构体中,`sin_port`需要在`UDP`协议中用到,`sin_addr`需要在`ip`协议中用到,因此需要将其转换为网络字节序.而`sin_family`只是被内核使用来据欸的那个数据结构中包含何种类型的地址,所以必须是本机字节序.

``` c++
struct sockaddr_in
  {
    __SOCKADDR_COMMON (sin_);
    in_port_t sin_port; //16位端口号,以网络字节序保存
    struct in_addr sin_addr;  //32位IP地址信息,以网络字节序保存,可以当作是32位整数

    /* Pad to size of `struct sockaddr'.  */
    unsigned char sin_zero[sizeof (struct sockaddr)
      - __SOCKADDR_COMMON_SIZE
      - sizeof (in_port_t)
      - sizeof (struct in_addr)];
  };

struct in_addr{
  In_addr_t s_addr;//32bit ipv4
}

```

#### sockaddr结构体
`sockaddr`结构体并非只是为了IPv4而设计,相当于是`sockaddr_in`的父类.

``` c++
struct sockaddr{
  sa_family_t sin_family;//地址族,必须清0
  char sa_data[14];//地址信息,包含套接字中的ip和端口号信息,其他补充0
}
```

#### sockaddr字节序转换(Endian Conversions)
常用于进行字节序转换的函数有:
- unsigned short htons(unsigned short);
- unsigned short ntohs(unsigned short);
- unsigned long htonl(unsigned long);
- unsigned long ntohl(unsigned long);

函数的命名规则为:h/n+to+n/h+type
n:网络字节序
h:主机字节序
l:long,32位,通常用于IP地址
s:short,16位,通常用于port转换

#### 点分十进制字符串IP地址转整数型IP
##### inet_addr()
使用`inet_addr()`函数可以将字符串形式的点分十进制IP地址转换成32位的整数型数据.其原型为:
``` c
#include <arpa/inet.h>
in_addr_t inet_addr(const char *string);
/*
成功时返回32位的大端序整数型值,失败时返回INADDR_NONE(可以检测无效的IP地址)
in:internet
*/
```

##### inet_aton()
使用`inet_aton()`也可以将字符型的IP地址转换成整数型ip地址.
``` c++
#include <arpa/inet.h>

int inet_aton(const char * string,struct in_addr * addr);
/**
string 点分十进制的ip地址
addr 保存转换后的ip地址的in_addr结构体的地址
return 转换成功?1:0
*/
```

所以一般的用法为:
``` c
if(!inet_aton(char * string,&sockaddr_in.sin_addr))
  error_handing("ip address conversion error")
```
##### inet_pton()
该方法类似于`inet-aton()`,但是不同之处在于,`inet_pton()`不仅支持IPv4协议地址转换,还支持IPv6.

## 数据交换
### UDP数据传输特性和connect函数
UDP是有数据边界的协议,传输过程中调用I/O函数的次数十分重要.输入函数的调用次数应该和输出函数的调用次数完全一致,这样才能保证接收全部已发送数据.

即UDP一次只能接收一个UDP数据报,无论接收方缓冲区开的有多大.

造成这个问题的原因主要为:
UDP是面向数据报的协议,而TCP是面向流的协议.也就是说TCP在发送时,会将大的数据进行拆包处理然后再进行传输,而UDP面向报文,因此每个UDP报文中有完整的报文头部,接收方容易对其进行处理.

总结:面向流传输和面向报文传输:
TCP:需要保证可靠传输,因此在报文接收发送之后需要反馈机制,如果是采用面向报文的方式,那么每次发包都需要进行验证,这会造成比较大的开销,而采用面向流的传输,则可以将多个包一起发送,减少了发包的数量及其开销.但是,如果进行频繁的数据传输,则会产生**粘包**问题.(多个数据包首尾相连)

粘包问题的处理
- 如果多个包是串行的,如一个文件的多个部分,那么粘包其实不用处理
- 如果多个包是不相关的,这个时候就需要处理粘包问题了

UDP不回产生粘包问题:有多少包,发送方就会调用多少次`sendto()`来发送数据,这就是**UDP保护消息边界**

example:
client调用了3次`sendto()`之后,server等待了5s之后再调用`recvfrom()`函数接收,如果是TCP程序,只需要调用一次接收函数,因此TCP是没有边界的,但是UDP需要调用3次`recvfrom()`函数,因为UDP是有边界的.


### 数据发送
UDP中使用`sendto()`函数进行数据IO.`sendto()`的定义为:
``` c
/** Send N bytes of BUF on socket FD to peer at address ADDR (which is
   ADDR_LEN bytes long).  Returns the number sent, or -1 for errors.

   This function is a cancellation point and therefore not marked with
   __THROW.

int fd:socket描述符
void * buf:要发送的数据地址
size_t n:待传输的数据长度,以Byte为单位
int flag:可选参数.若无则传递0
addr:保存客户端地址信息的sockaddr地址
addr_len:客户端地址结构体的长度

  */
extern ssize_t sendto (int __fd, const void *__buf, size_t __n,
		       int __flags, __CONST_SOCKADDR_ARG __addr,
		       socklen_t __addr_len);
```

### 数据接收

### connected/unconnected udp socketed
TCP套接字中需要注册待传输数据的目标IP和端口号,但是UDP则无需注册.使用`sendto()`发送数据的大致过程主要有三个阶段:
1. 向UDP套接字注册目标IP和端口号
2. 传输数据
3. 删除UDP套接字中注册的目标地址信息

每次调用`sendto()`函数都会重复以上过程.

connected socket:已经注册了地址信息的套接字
UDP 套接字属于`unconnected socket`.

但是这样有一个问题:如果一次有多个数据要发送,则需调用多次sendto进行传输.这种情况下,使用`connected socket`就将会减少多次调用sendto产生的连接注册/取消的代价.

即:调用`connect()`函数向UDP套接字注册IP和端口等地址信息,而不是要和对方UDP套接字创建连接.



# 异步IO

## select函数

select函数用于在**非阻塞**中，当一个套接字或一组套接字有信号时通知你，系统提供select函数来实现多路复用输入/输出模型，原型：    

``` c
#include <sys/time.h>
#include <unistd.h>
/**
@param int maxfd:maxfd是需要监视的最大的文件描述符值+1
@param fd_set *rdset:可读文件描述符集合
@param fd_set *wrset：可写文件描述符集合
@param fd_set *exset：异常文件描述符集合
@param struct timeval结构用于描述一段时间长度，如果在这个时间内，需要监视的描述符没有事件发生则函数返回，返回值为0。(如时间是超时时间设置为5s)
*/
int select(int maxfd,fd_set *rdset,fd_set *wrset,fd_set *exset,struct timeval *timeout);
```

 重要的数据结构：

是一组文件描述字(fd)的集合，**它用一位来表示一个****fd**

对于fd_set类型通过下面四个宏来操作：

- FD_ZERO(fd_set *fdset);**将指定的文件描述符集清空，在对文件描述符集合进行设置前，必须对其进行初始化**，如果不清空，由于在系统分配内存空间后，通常并不作清空处理，所以结果是不可知的。

- FD_SET(int fd, fd_set * fdsetp);用于在文件描述符集合中**增加**一个新的文件描述符。

- FD_CLR(int fd, fd_set * fdsetp)用于在文件描述符集合中**删除**一个文件描述符。

- FD_ISSET(int fd,fd_set *fdset);用于测试指定的文件描述符是否在该集合中。

errno的含义：

| 值    | 含义           |
| ----- | -------------- |
| EINTR | 接收到中断信号 |
|       |                |
|       |                |

example：



# 重要数据结构

## msghdr

struct msghdr：存储接收或者要发送的消息的元数据，如地址信息，消息指针等

iovec：用于io的数据结构，成员主要是数据指针和数据的长度

``` c
struct iovec
  {
    void *iov_base;	/* Pointer to data.  */
    size_t iov_len;	/* Length of data.  */
  };
```

