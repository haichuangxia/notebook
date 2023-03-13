# proto-quic项目中的结构

``` mermaid
classDiagram


class QuicPacketGenerator{
  +QuicFramer* framer_;
  +QuicBufferAllocator* const buffer_allocator_;
	-QuicPacketCreator packet_creator_;
  -QuicFrames queued_control_frames_;
    +QuicConsumedData ConsumeData()
    +QuicConsumedData ConsumeDataFastPath()
}

class QuicPacketCreator{
  +QuicFramer* framer_;
  +QuicBufferAllocator* const buffer_allocator_;
  +QuicFrames queued_frames_;
 +void CreateAndSerializeStreamFrame()
	 +bool ConsumeData()
	  +bool AddSavedFrame()
	   -void CreateStreamFrame()
}

class QuicStreamFrame{
+QuicStreamFrame()
}
class QuicFramer{
 +BuildDataPacket()
}

class QuicStream{
+WriteOrBufferData();
+WriteStreamData();
}

class QuicStreamSendBuffer{
+void SaveStreamData();
}


QuicStream ..|> QuicStreamSendBuffer
QuicStream ..|>QuicStreamSequencer

QuicPacketGenerator ..|> QuicPacketCreator
QuicPacketCreator ..|> QuicStreamFrame
QuicPacketCreator ..|> QuicFramer
QuicPacketCreator ..|> QuicBufferAllocator
QuicPacketCreator ..|> QuicFrame
QuicPacketCreator ..|> SerializedPacket
QuicFramer ..|> QuicDataWriter
QuicConnection ..|> QuicFramer
QuicConnection ..|> QuicPacketWriter
QuicDispatcher ..|> QuicBufferedPacketStore
QuicPacketWriter ..|> QuicSocketUtils
QuicConnection ..|> QuicSentPacketManager
QuicConnection ..|>   QuicPacketGenerator
QuicClient ..|> QuicConnection
QuicClient ..|> QuicSession
QuicClient ..|> QuicConfig
QuicClient ..|> QuicPacketWriter

QuicServer ..|> QuicDispacther
QuicServer ..|> QuicPacketReader
```

说明:在QuicPacketGenerator的构造函数中,会根据输入的`QuicFramer`和`QuicBufferAllocator`等参数,构造一个`QuicPacketCreator`对象

`quicPacketcreator`对象,先构造一个queued<Frame>,通过`addFrame`方法向队列中添加包,然后调用`quicFramer->buildDataPacket()`方法来构造包

`QuicFramer`对象中,`BuildDataPacket()`方法中根据``buffer``参数进行构造`QuicDataWriter`



由`quic_sent_packet_manager`类调用拥塞控制等算法进行数据发送



`QuicStreamSendBuffer`提供的函数:该函数使用的data_writer由调用者给出,而非自己产生

| 函数            | 作用                                                         |      |
| --------------- | ------------------------------------------------------------ | ---- |
| WriteStreamData | 使用datawriter对象,将缓冲区内的dataslice(不是帧,没有stream id)封装成帧,并打包成packet |      |
|                 |                                                              |      |
|                 |                                                              |      |



关于datawritrer:

quic_packet_creator->buildDataPacket()方法中,会根据已有的参数,构造datawriter



关于quic中的socket:

管理socket的实体为:`QuicDispatcher`

| 文件                         | 作用                                            |
| ---------------------------- | ----------------------------------------------- |
| quic_buffered_packet_store.h | socket中,用于缓存还没有connection来管理的packet |
| quic_socket_utils.h          | 为quic提供一些socket相关的helper函数            |
|                              |                                                 |

核心的文件夹:

1. net/quic/core
2. net/tools/quic
3. net/socket 封装了socket通信

通信时,需要先握手建立连接,在握手过程中就会创建connection,然后再根据请求创建stream



Initial数据包包号是0



**connection**和**stream**之间通过**session**对象来进行管理,即:**session**用于负责创建和管理**stream**

