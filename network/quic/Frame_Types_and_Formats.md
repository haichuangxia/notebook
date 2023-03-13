# packet

## packet首部类型

| 字段                 | 字段名   | 位置                               | 长度 | 解释                                                                                                                  | 掩码 |
| -------------------- | -------- | ---------------------------------- | ---- | --------------------------------------------------------------------------------------------------------------------- | ---- |
| Reserved bits        | 保留位   | 字节0                              | 2bit | 初始值为0,若收到不为0则表示`PROTOCOL_VIOLATION`类型错误                                                               | 0x0c |
| Packet Number Length | 包号长度 | 字节0                              | 2bit | 在包含Packet Number字段的数据包类型中，字节0的最低两个有效位（掩码为0x03）表示Packet Number字段的长度.有4中长度的包号 | 0x03 |
| length               | 长度     |                                    | 变长 | packetNumber+packet Playload部分的长度,是数据包剩余部分                                                               |      |
| packet number        | 包号     | 1-4字节(但是后面也有1,2,4,8的长度) |      | 数据包的有效负载,包含一系列帧                                                                                         |      |

## packet类型


``` mermaid
classDiagram 
class 0_RTT_packet {
 Header Form [1] = 1,
  Fixed Bit [1] = 1,
  Long Packet Type [2] = 1,
  Reserved Bits [2],
  Packet Number Length [2],
  Version [32],
  Destination Connection ID Length [8],
  Destination Connection ID [0..160],
  Source Connection ID Length [8],
  Source Connection ID [0..160],
  Length [i],
  Packet Number [8..32],
  Packet Payload [8..],
  }

class Retry_Packet {
  Header Form[1]= 1,
  Fixed Bit [1] 1,
  Long Packet Type [2] = 3,
  Unused [4],
  Version [32],
  Destination Connection ID Length [8],
  Destination Connection ID [0..160],
  Source Connection ID Length [8],
  Source Connection ID [0..160],
  Retry Token [..],
  Retry Integrity Tag [128],
}

 class version_negotiation_packet {
     Header Form [1] = 1,
     Unused [7],
     Version [32] = 0,
     Destination Connection ID Length [8],
     Destination Connection ID [0..2040],
     Source Connection ID Length [8],
     Source Connection ID [0..2040],
     Supported Version [32] ...,
   }
   
class Initial_Packet {
  Header Form [1] = 1,
  Fixed Bit [1] = 1,
  Long Packet Type [2] = 0,
  Reserved Bits [2],
  Packet Number Length [2],
  Version [32],
  Destination Connection ID Length [8],
  Destination Connection ID [0..160],
  Source Connection ID Length [8],
  Source Connection ID [0..160],
  +Token Length [i] //变长整数,token的长度以字节为单位
  Token [..],
  Length [i],
  Packet Number [8..32],
  Packet Payload [8..],
}

class Handshake Packet {
  Header Form (1) = 1,
  Fixed Bit (1) = 1,
  Long Packet Type (2) = 2,
  Reserved Bits (2),
  Packet Number Length (2),
  Version (32),
  Destination Connection ID Length (8),
  Destination Connection ID (0..160),
  Source Connection ID Length (8),
  Source Connection ID (0..160),
  Length (i),
  Packet Number (8..32),
  Packet Payload (8..),
}

```
短包头类型为:
``` mermaid
classDiagram
class 1_RTT_Packet {
  Header Form (1) = 0,
  Fixed Bit (1) = 1,
  Spin Bit (1),
  Reserved Bits (2),
  Key Phase (1),
  Packet Number Length (2),
  Destination Connection ID (0..160),
  Packet Number (8..32),
  Packet Payload (8..),
}
```


| 包头长度 | 包类型              | 作用                                                         |
| -------- | ------------------- | ------------------------------------------------------------ |
| 长包头   | version negotiation | 进行版本协商,客户端收到后，将Version字段值为0的数据包识别为Version Negotiation包。Version Negotiation包仅由服务端发送，是在收到不支持版本的客户端数据包时回的响应。 |
| 长包头   | initial             | **携带客户端和服务端发送的第一个CRYPTO帧以执行密钥交换，双向都可携带ACK帧** |
| 长包头   | 0-RTT               |                                                              |
|          | retry               | 携带由服务端生成的地址验证令牌,仅由希望进行重试的服务端使用  |



# packet类型

| packet type          | 特点                          | 定义                                       |
| -------------------- | ----------------------------- | ------------------------------------------ |
| Ack-eliciting Packet | 发送方接收到包之后需要发送ack | 不含ack,padding,connection_close帧的数据包 |
|                      |                               |                                            |
|                      |                               |                                            |



## packet头部

``` mermaid
classDiagram
class QuicPacketPublicHeader{
    QuicConnectionId connection_id;              // 8字节
    QuicConnectionIdLength connection_id_length; //枚举类型,最大值8
    bool reset_flag;                             //布尔类型,1byte
    bool version_flag;
    QuicPacketNumberLength packet_number_length; // int8_t,1byte
    QuicVersionVector versions;
}

class QuicPacketHeader{
    QuicPacketPublicHeader public_header;
    QuicPacketNumber packet_number; // 8byte的包序号
}
class Quicdata{
    const char *buffer_;
    size_t length_;
    bool owns_buffer_;
}
class QuicPacket{
quicdata；
quicPacketHeader；
}
QuicPacketPublicHeader --o QuicPacketHeader
Quicdata --o QuicPacket
QuicPacketHeader --o QuicPacket
```

## 两种packet头部

### short packet header

``` mermaid
classDiagram
class 短包头{
Header Form [1] = 0,
  Fixed Bit [1] = 1,
  Spin Bit [1],
  Reserved Bits [2],
  Key Phase [1],
  Packet Number Length [2],
  Destination Connection ID [0..160],
  Packet Number [8..32],
  Packet Payload [8..],
}
```

### long packet header


# Frame

一个**packets**可以包括多个**frames**。本节主要是讨论**QUIC**帧的格式及其语义。几种帧的作用如下表：

| 帧                         | Type            | 作用                                                         | 大小(byte) | 特点   |
| -------------------------- | --------------- | ------------------------------------------------------------ | ---------- | ------ |
| PADDING frame              |                 | 填充，使得数据包达到规定的最小值                             | 1          | 无内容 |
| PING frame                 |                 | 其对端是否仍然存在或检查对端的可达性。                       | 1          | 无内容 |
| ACK Frames                 |                 |                                                              |            |        |
| STOP_SENDING Frames        |                 | **STOP_SENDING请求对端停止在该流上发送数据**                 |            |        |
| CRYPTO帧                   |                 | **传输加密握手消息**                                         |            |        |
| NEW_TOKEN帧                |                 | 给客户端提供一个令牌，以便在未来连接发送Initial包时在报文中携带 |            |        |
| STREAM Frames              |                 | **隐式地创建一个流并携带流数据**                             |            |        |
| MAX_DATA Frames            | **0x10**        | **用于流控，以通知对端其可以在整个连接上发送的最大数据量。** |            |        |
| MAX_STREAM_DATA            | 0x11            | 用于流控，以通知对端其可以在该流上发送的最大数据量。         |            |        |
| MAX_STREAMS                | 0x12            | 双向流,通知**对端其允许打开的给定类型的流的累积个数**        |            |        |
|                            | 0x13            | 单向流,通知**对端其允许打开的给定类型的流的累积个数**        |            |        |
| DATA_BLOCKED Frames        | 0x14            | DATA_BLOCKED帧可用作流控算法调整的输入,指示阻塞发生时的连接级别的流量限制值。 |            |        |
| STREAM_DATA_BLOCKED Frames | 0x15            | 流级别,指示发生阻塞时流的偏移量.                             |            |        |
| STREAMS_BLOCKED            | Type=0x16或0x17 | 发送方想打开的流数量超出了设置的最大个数限制,                |            |        |
| NEW_CONNECTION_ID Frames   | Type=0x18       | 为其对端提供替代CID，这些CID可用于在迁移连接时打破可关联性   |            |        |
| RETIRE_CONNECTION_ID       | Type=0x19       | **指示它将不再使用由其对端发布的指定CID**                    |            |        |
| PATH_CHALLENGE             | 0x1a            | 检查对端的可达性以及用于连接迁移期间的路径验证。             |            |        |
| PATH_RESPONSE              |                 | 以响应PATH_CHALLENGE帧                                       |            |        |
| CONNECTION_CLOSE Frames    |                 | 通知其对端连接正在关闭                                       |            |        |
| HANDSHAKE_DON              |                 | 向客户端发出握手确认信号                                     |            |        |

## **PADDING** Frames
没有其他特殊的含义，不过可以用来增加`packet`的大小。如果一个数据包过小，没有达到最小值，或者需要提供对抗流量分析，就需要使用这种帧。
``` C++
PADDING Frame {
Type (i) = 0x00,
}
```

##  **PING** Frames
主要的作用就是，看对方是否还在（alive），是否可达（reachability）。**PING**帧的格式为：
``` C ++
PING Frame {
Type (i) = 0x01,
}
```
## ACK帧
``` c++
ACK Frame {
  Type (i) = 0x02..0x03,
  Largest Acknowledged (i),
  ACK Delay (i),
  ACK Range Count (i),
  First ACK Range (i),
  ACK Range (..) ...,
  [ECN Counts (..)],
}
```