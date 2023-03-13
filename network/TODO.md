每个节点存储随机数,发送器需要存储所有的随机数

现在:函数计算,每个节点,**hmac算法**来计算每个随机数,参数为:密钥,发起方知道,另一个参数为字符串,给每个节点一个编号(二叉树上每个节点编号不同,),发起方知道所有节点,发送方通过**安全信道**发送给接收方,接收方就有秘密了,

会话密钥,需要是使用组密钥加密,组密钥也是可以选的,子节点记录组的根节点,发送的时候根据组的编号来发送



**AES**算法



k辅密钥与会话密钥K的对应关系



Tor:使用

公私钥对作为id,使用非对称方式,发送private information

# 实现广播加密算法

1. 树怎么建立,怎么进行编号 -- 使用哈夫曼编码?暂时不考虑节点的新增和删除,单纯的静态的节点管理

## 约定

| key         | value                    |
| ----------- | ------------------------ |
| session key | 128位,使用string格式保存 |
|             |                          |
|             |                          |



## 整体交互流程

``` mermaid
sequenceDiagram
	participant s as Server
	participant c as Receiver
	Note left of s:初始化建树
	c ->>s:request
	Note left of s:为receiver分配空闲二叉树叶子节点
	s ->> c:返回I_u
	note left of s:子集划分
	
```



## server端
### TreeNode
``` mermaid
classDiagram
class TreeNode{
+int nodeId;
+uint128_t secret;
}
```

### Message

server向receiver发送的消息,包括合法用户分组,密钥加密的session key,HMAC值以及消息明文.应提供get()方法,以供receiver使用其成员变量.

``` mermaid
classDiagram
class Message{
	-int[] im;
	-string[] session_key_encrypted;
	-string hmac_value;
	-string plaintext;
	+Message(im,session_key_encrypted,hmac_value,plaintext);
}
```

### Server
``` mermaid
classDiagram
class Server{
int m;//合法用户子集数
std::list<Subset> im;//合法用户子集
}

```

| 函数                 | 作用                                             |
| -------------------- | ------------------------------------------------ |
| buildTree()          | 初始化完全二叉树                                 |
| userInitialization() | 为用户分配叶子节点,返回给用户private information |
| csSubsetDivision()   | 在广播消息前进行子集划分                         |



## client

private infromation设计

`typedef std::map<nodeId_t, longKey_t> IU;`

``` mermaid
classDiagram
class iuItem{
+int nodeId;
+uint128_t longLivedKey;
}

```

1. 使用map<>的存储结构可以根据nodeId快速找到其对应的$L_i$ 

### client设计

对于server发送的消息message的解析:

``` mermaid
classDiagram
class message{
	-uint8_t m;
	-nodeId_t[] sets;
	-uint128_t[] longLivedKeys;
    -String cipherMessage;
    + toString();
    +send();
}
```



记录其在server的二叉树中的节点编号,方便其快速查找其所有的祖先节点

``` mermaid
classDiagram
class Client{
uint16_t nodeId;
std::map<nodeId_t, longKey_t> IU;
uint16_t isInSet();
}
```

| 函数             | 作用                                                         |
| ---------------- | ------------------------------------------------------------ |
| isInSet()        | 是否属于合法子集,如果是则返回祖先节点id                      |
| client()         | 向server申请加入完全二叉树,并获得private information以及自身的节点编号 |
| getSessionKey()  | 根据private information获取LongLivedKey,然后获取会话密钥Session Key |
| decryption()     | 使用Session Key对消息*M`*进行解密                            |
| processMessage() | 处理server发送的消息封装成类                                 |

# 文件设计

| 文件    | 作用                                                       |
| ------- | ---------------------------------------------------------- |
| message | 封装一个消息,                                              |
| payload | 申请缓冲区,从文件或者用户输入中获取数据,返回一个缓冲区指针 |
| Iu      | 向接收者提供的分组信息以及相关的密钥                       |



# Tor:洋葱路由

使用子集的$L_i$对路由信息进行加密,则当前receiver则只能通过$L_i$解密出下一条的地址,而不能获取更多的信息
