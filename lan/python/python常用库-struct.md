  struct模块主要用来完成**数值**与**字符串**(python中没有字节,因此这个这个字符串实质就是相当于字节流)之间的格式转换

### API

1. string pack(fmt,v1....)

   将指定的数据对象转换称为二进制的字节流,使其可以在网络环境下传输

   1. 参数

      1. fmt:要返回的字符串格式,类似C中的标准输入输出语法
      2. v 要转换成字节流的python数据类型
      3. return 返回一个字符串

   2. 用法
        ``` python
          import struct
          a=20
          b=400
          str=struct.pack('ii',a,b)
          print(str)
        # output:b'\x14\x00\x00\x00\x90\x01\x00\x00'
        ```

2. unpack()

   1. 将二进制的字节流格式化为指定的数据类型