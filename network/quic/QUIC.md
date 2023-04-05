# 概述
quic:面向连接
quic握手:加密+传输参数协商,继承了TLS
endpoint通过交换quic package通信,package中包含frame

``` mermaid
classDiagram
class QuicFrame{
   控制信息
   应用数据
}

```

quic的任务:
1. 验证package完整性
2. 尽可能对package进行加密


# 流
**流**是**QUIC**向上层应用提供的**轻量**,**有序**的字节流抽象.**流**可以通过**发送数据**创建,其他的数据流管理,如:终结,取消,管理流控制(managing flow control),只会增加少量的开销.

单个**STREAM**帧可以打开流,携带数据并关闭流.

流需要接收流约束和限制.
## 流的类型和标识符
从数据流的方向上,有两种类型:
1. 单向流:沿着流的初始方向,从起点到终点
2. 双向流:数据可以双向传播

使用`streamID`(62bit的数字串)来对流进行唯一标识.同一个连接中,`streamID`不可重复.`streamID`的编号规则为:
1. 最低有效位:表明流的发起者,
2. 次低有效位:表明流的方向(单向 OR 双向)



## 流中收发数据

应用发送的数据被封装到帧中.通过**StreamId**和**Offset**字段来实现数据有序化.为了实现数据的有序到达,需要有一定的**缓冲区**来保存失序到达的字节.

若干一个数据以相同的**offset**发送了多次,则接收端只会保存**最近一次**接收到的数据.

**流**是**有序**的**字节流**的抽象,对于QUIC来说,它看不见除了**流**之外的其他结构.

## 流的优先级
**QUIC**并不提供交换优先级信息的机制,而是从应用处接收优先级信息.

**QUIC**的实现方案,**应该**确定由哪个应用来提供流的优先级信息,并根据这些流的优先级为其分配资源.


## 流的操作
**QUIC**并不提供流的操作API,而是定义了流的操作接口,需要根据不同的环境来实现接口.在数据传输的不同阶段,流需要提供不同的操作

### 流发送阶段
1. 写入数据,在流控制中寻找一个可靠时间来发送数据
2. 终止数据流:在**STREAM** frame中通过添加**`FIN`**字段来设置
3. 重置流:通过使用**RESET_STREAM**帧来实现

### 流接收阶段
1. 读取数据
2. 中止读取流,可能造成**STOP_SENDING**帧

### 连接中
要求在连接中能够获知流的状态变化,包括:
   1. 连接的另一方合适开启或者重置流
   2. 对方何时中止读取流
   3. 新的数据何时可用
   4. 因为流控制,数据何时能否被写入

# 流的状态
数据的接收端和发送端各有不同的**有限状态机**.两种状态机,**单向流**必须二者选其一,具体选什么需要根据
1. 流的类型
2. 终端的角色(initator or other)

**双向流**两端可以同时使用两种状态机.

## 发送端有限状态机
发送端的有限状态机示意图为:




各个状态的作用为:
| 状态       | 作用                                                                           |
| ---------- | ------------------------------------------------------------------------------ |
| ready      | 表示流是新创建的,可以接收上层应用数据，准备发送数据                            |
| Send       | 为流分配StreamID，终端传输`STREAM` frame，在次过程中，需要被**流管理**机制控制 |
| Data Send  | 此状态用来进行必要的重传                                                       |
| Data Recvd | 表示所有数据已经收到ACK，传输完毕                                              |
## 接收端有限状态机
接收端的有限状态机状态为：



**在流创建之前，所有同类型的，StreamID数值更小的流必须被创建。**

| 状态        | 含义                                                                                                                                             |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| Recv        | 接收**STREAM**，**STREAM_DATA_BLOCKED**帧，接收到的帧进行按序重组，当数据被上层提取，缓冲区可用时，发送**MAX_STREAM_DATA**帧让对方发送更多的数据 |
| Size known  | 当收到**带有FIN**的STREAM帧时，代表数据发送完毕，此时就知道了数据的完整大小。此阶段：1. 不发送**MAX_STRTEAM_DATA**帧，2： 只接收重传的数据       |
| Data Recvd  | 表示所有的数据已经收到，后面收到的帧将会被舍弃 ，该状态将持续到所有数据传递给应用                                                                |
| Reset Recvd | 收到**RESET_STREAM**帧时进入该状态，该状态将会中止向应用传输数据                                                                                 |

## Permitted Frame Types
流的发送方只能发送3种可以改变发送端或者发送端状态的帧：
1. STREAM
2. STREAM_DATA_BLOCKED
3. RESET_STREAM

发送端在`terminal state`(**Data Recvd** or **Reset Recvd**)不能发送以上的三种帧。但是接收端可以在任何状态接收这三种帧（因为有延迟的存在）。

**MAX_STREAM_DATA**只能在**Recv**状态由接收端发送。接收方可以在**任何**状态发送**STOP_SENDING**帧。

##  Solicited State Transitions(请求状态转换)
Solicited：请求。

如果应用不想继续接收数据，此时就可以停止读取数据并给出一个错误代码。此时，如果流正处于**Recv**或者**Size Known**状态，需要发送**STOP_SENDING**帧来停止流。在发送**STOP_SENDING**之后接收到的**STREAM**帧仍然继续计数，即便是后面有可能会丢弃。


**STOP_SENDING**帧要求帧的接收方发送一个**RESET_STRTEAM**帧，帧的接收方处于不同状态时，发送**RESET_STREAM**帧的时间不同
1. 若处于**READY**或者**Send**状态，那么就必须发送这个帧。
2. 若处于**Data Sent**状态，那么需要等到数据包所包含的数据被确认收到或者丢失后再发送**RESET_SENDING**帧。（丢失的数据不再重传，而是直接发送`RESET_SENDING`帧）

注意事项：
1. 终端需要将**STOP_SENDING**中的`error code`装入到**RESET_STREAM**帧中（也可以使用application error code）
2. **STOP_SENDING**只能在没有`reset`时发送，通常在**RECV**或者**Size Known**状态使用
3. 如果**STOP_SENDING**丢失了，需要重发。但是如果所有数据都接收到了，即不在**Recv**和**Size Known**状态了，那么久没有必要重发了

# 流量控制
流量控制（flow control）主要的目的是控制流的发送速度,防止发送方发送过快，接收方缓冲区溢出。


## 数据流控制
**QUIC**中采用基于限制的流量控制方法，接收方会限制自己在**单个流**或**整个连接**所接收到的字节流。针对不同的限制对象，存在两种不同级别的数据流控制：
1. Stream flow control：限制每个流可以发送的数据量
2. Connection flow control：限制整个连接持续期间，所有流发送**STREAM**帧中的字节数。

流控制的过程为：
1. 在握手期间，接收方通过传输参数为所有流设置初始化限制
2. 接收方通过**MAX_STREAM_DATA**帧或者**MAX_DATA**帧来告诉发送方设置更大的限制，如果是并不增加limit的帧，则sender会忽略


### 1. 通过帧设置limit
接收方可以通过两种帧告知发送方还可以发送多少数据.
| 帧              | 类型   | 作用                                       |
| --------------- | ------ | ------------------------------------------ |
| MAX_STREAM_DATA | 控制帧 | 表明**一个流**的最大绝对偏移量             |
| MAX_DATA        | 数据帧 | **所有流**的最大绝对偏移量的综总和的最大值 |

### 2. 超出limit的处理方案
如果发送方发送的数据超过了约定的`limit`,则**接收方****必须**使用**FLOW_CONTROL_ERROR**类型的错误来关闭连接。

如果发送者仍然需要发送，但是已经达到`limit`了，那么就需要锁住流或者连接，发送者应该发送**STREAM_DATA_BLOCKED**或者**DATA_BLOCKED**帧来表明自己仍然有数据需要发送，但是被flow control锁住了。但是如果锁的时间太长，那么接收方就会关闭连接。为了防止关闭连接，发送方就需要在没有`ack-eliting packedt` in flight时发送**STREAM_DATA_BLOCKED**或者**DATA_BLOCKED**帧

## 增加流控制限制
为了防止发送方被锁住，接收方需要在一个传输轮次之内多次发送**MAX_STREAM_DATA**或者**MAX_DATA**帧，或者尽可能早的发送，以应对帧的丢失已经后续的恢复。

`limit`的改变需要使用控制帧，控制帧发送过多，就会增加开销，如果发送少，就需要一次设置较大的limit值，避免发送方被锁住，但是这又会造成更大的资源开销，如接收方的缓冲区。因此，接收方采用了一种类似TCP的自动调节机制来调节控制帧的发送频率等（基于估算的`round-trip time`和接收方的数据消耗速率）

## 流量控制性能
通过讲**流控帧**和其他帧,如**ACK**帧一起发送会提高性能.
## Stream Final Size(流最终大小)
`final size`是流量控制机制认为的，被流消耗的数据总量，可以认为是数据的字节数，这个值一般来说，大于流中帧的最大偏移量，如果没有字节发送，那么该值为0.

final size的值满足：
$$
final\ size \eqcirc \sum{Offset} = Length\ fields\ of\ a STREAM\ frame\ with\ a\ FIN\ flag
$$

当接收端进入**Size Known**或者**Reset Recvd**状态时，接收方就会知道**final size**的值。接收方**必须**使用**final size**来核验连接层面的流量控制中，各个**STREAM**发送的数据量总和对不对，一个终端**不能**发送超过`final size`的数据。

一旦`final size`确定了就不能更改。如果**RESET_STREAM**或者**STREAM**帧表明`final size`发生了变化，则终端需要报`FINAL_SIZE_ERROR`错误。

## Controlling Concurrency（并发控制）

终端限制了对方可以打开的最大`incoming streams`，只有满足
$
streamID \leq max\_streams \times 4+first\_stream\_id\_of\_type
$
的流才可以被打开。


初始的`limits`通过`transport parameters`来设置，后续的值通过`MAX_STREAMS`帧来设置。

# 常见的各种错误
| 错误名                    | 错误含义                                                               |
| ------------------------- | ---------------------------------------------------------------------- |
| TRANSPORT_PARAMETER_ERROR | `max_streams transport parameter`，实际上不能设置这么大的参数          |
| FRAME_ENCODING_ERROR      | `MAX_STREAMS frame`中最大streams设置的过大，实际上不能设置这么大的参数 |
| STREAM_LIMIT_ERROR        | streamID超过允许的最大值                                               |

# 连接
一个**QUIC**连接通过握手建立（使用一个加密协议来握手-QUIC-TLS），握手阶段确定连接的相关参数并建立连接。

## Connection ID

connection id不能包含任何可以被第三方获取到的信息。

### Issuing Connection IDs（颁发）

### Matching Packets to Connections
数据包在被收到的时候进行分类。一个连接可以有多个connection id与之对应。

## Operations on Connections
不是定义接口，而是定义需要实现的功能，由QUIC的实现方来实现这些功能。

# Version Negotiation（版本协商）
主要是服务器用来表明客户端使用的版本中，有哪些是服务器不支持的。

# 7. Cryptographic and Transport Handshake(加密和传输握手)

**QUIC**使用了**QUIC-TLS**协议来进行加密，使用**CRYPTO**帧来传输加密的握手信息，不同版本的QUIC协议可以使用不同的加密协议。

握手阶段C-S之间交换的数据有：
- 认证密钥
- 数据传输的参数

握手过程图为：

**Handshake**和**Initial**都是一种**packet**。

## Example Handshake Flows
## Negotiating Connection IDs
## Authenticating Connection IDs
## Transport Parameters
## Cryptographic Message Buffering

# 8. 地址验证

进行地址验证的主要目的是：防止一个终端被用来做`traffic amplification attack`（一个数据包假冒受害者的地址，使得服务器难以处理（spoofing attack））。--用来对抗**amplification attack**。

**amplification attack**：假冒受害者的地址向服务器发送数据包，让服务器向受害者发送大量数据。

通过地址验证来对抗**amplification attack**的基本原来：验证对方是否能用声称的地址来接收数据包。接收到没有验证过地址的终端所发送的数据时，服务器**必须**限制其发送到未验证的地址为其接收的数据量的三倍。($发送量 \leq 3\times 接受量$）.

这个允许发送的数据量大小也叫做：**anti-amplification limit**

地址验证过程的时间：
1. 连接建立时
2. 连接迁移时（connection migration）

## 连接建立期间地址验证
## 路径验证
用来在**connection migration**期间，在地址变化之后，双方验证地址可用性。地址时IP地址和端口号所构成的二元组。

# 连接迁移（Connection Migration）
`connection ID`的存在使得连接不以IP地址和端口号作为连接的标志，因为有`connection ID`的存在，即使连接双方更换了IP地址和端口号连接也仍然存在，例如一方更换了网络环境。

`connection migration`研究的就是，如果实现IP地址的更换。但是有一点需要注意：只有在握手完成之后才能进行`connection migration`

长头部的数据包有：Connection ID和Destination ID两个字段。这两个字段用来进行连接的迁移。

## Probing a New Path

通过使用路径验证来探测路径

## Initiating Connection Migration

## Responding to Connetion Migration

接收一个从对方的新地址发出的，包含未探测帧则表明对方已经迁移到了新地址。如果接收方同意对方进行地址迁移，则接收方**必须**发送后续的数据包到新地址，并且**必须**进行路径验证，以证明新地址确实属于对方。

## Loss Detection and Congestion Control
新旧路径上的可用容量（capacity available）不一定相同，因此需要重新测算，以免造成拥塞或者增加RTT。

在进行地址确认的同时，也需要重置拥塞控制器和RTT估计器（除非只是更换了端口号）。

## Privacy Implications of Connection Migration
终端不希望自己的活动同除了quic连接的peer产生关联。因此，从不同的本地地址建立连接有时候需要使用不同的`connection ID`

## Server`s Preferred Address
**QUIC**允许服务器接收一个IP地址上的连接，并在握手后尝试将这些连接迁移到一个更首选的地址。

# Connection Termination
有三种方法可以结束一个**QUIC**连接：
1. idle 超时
2. 立即关闭
3. 无状态重置

## Idle Timeout
`idle timeout`:空闲超时，如果一个连接长时间误无操作，超出了一定的时间，那么就会断开连接。

## Immediate Close
通过发送**CONNECTION_CLOSE**帧来立即停止一个连接。

# 错误处理
## 连接错误
## 流错误

# 数据包和帧
存在两种不同长度的数据包：
1. Packets with the long header
2. Packets with the short header

一个数据包可以拥有多个帧
``` mermaid
classDiagram
class QuicFrame{
   STREAM ID
   Ofsset 按序封装数据
}
```

不同帧的作用
| 帧名称              | 作用                                                                   |
| ------------------- | ---------------------------------------------------------------------- |
| STREAM              | 影响发送方或接收方的流状态                                             |
| STREAM_DATA_BLOCKED |
| RESET_STREAM        |
| MAX_STREAM_DATA     | 接收方通告流的发送方指流新的流量限制(理解成窗口大小)(Offset值得最大值) |
| MAX_DATA            | 指定所有流绝对字节偏移总量的上限                                       |
| STOP_SENDING        |

作用对象是全部流的集合的帧
| 帧       | 作用                                 |
| -------- | ------------------------------------ |
| MAX_DATA | 所有流可以发送数据偏移量之和的最大值 |

不同阶段可以发送的帧:
流的发送方:
|     | DATA Recvd | Reset recvd |
| --- | ---------- | ----------- |
# 名词解释
| 名词         | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| 0RTT         | 通信双方的第一个数据包就可以携带有效数据                     |
| 长包头数据包 | **在确立1-RTT密钥之前收发的数据包**                          |
| 0-RTT数据包  | **0-RTT包用于将early数据从客户端发往服务端，在Handshake完成之前**,是TLS握手的一部分 |
| 1-RTT数据包  | **1-RTT包使用短包头**。它**在版本协商和****1-RTT秘钥协商后使用** |