洋葱路由的基本组件：

![image-20230214214000648](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230214214001.png)

``` mermaid
graph LR
Tor --> 用户
用户 --> OP[代理客户端: 将用户进行封装成Tor信元并加密]
用户 --> 隐藏服务器["隐藏服务器：提供web等TCP服务的服务器，只有TOR能访问"]
Tor --> node[中继节点]
node --> entry["入口节点：一般选择可信度较高的Guard节点"]
node --> middle[中间节点]
node --> exit[出口节点]
Tor --> manage
manage --> dics["目录服务器:保存所有洋葱路由器的IP地址和带宽等信息"]
manage --> hsds["隐藏目录服务器：提供隐藏服务器的引入节点，公钥等节点新"]
```



Tor匿名通信网络结构

![image-20230215205349376](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230215205350.png)

流程：

1. 隐藏服务器选择**3 个**洋葱路由器作为其**引
   入节点（IPO）**，并与引入节点建立3 跳链路;
2. 隐藏服务器将其**隐藏服务描述符(hidden
   service descriptor)** 上传至**隐藏服务目录服务器**，描
   述符中包含引入节点的信息与自身RSA 公钥;
3. 客户端通过隐藏服务域名(<z>.onion) 进行
   访问时，从**隐藏服务目录服务器**获取引入节点的相
   关信息;
4. 客户端选择**一个**洋葱路由器作为**汇聚节点（RPO）**
   并与该节点建立3 跳链路;
5. 客户端建立到达**引人节点**的**3 跳链路**，并通
   过引人节点将**汇聚节点**的信息发送到隐藏服务器;
6. 隐藏服务器建立到达汇聚节点的3 跳链路，
   并对该链路进行认证;
7. 经过汇聚节点，客户端与隐藏服务器通过6
   跳链路进行交互.

``` mermaid
sequenceDiagram
autonumber
note over 隐藏服务器:选择IPO并建链
隐藏服务器 ->> 隐藏服务目录服务器:domain.onion的隐藏服务描述符<IPO1,IPO2,IPO3,公钥>
客户端 ->> 隐藏服务目录服务器:隐藏服务器域名domain.onion
隐藏服务目录服务器 ->>客户端:domain.onion的IPO等信息
note over 客户端:选择RPO并建链
客户端 ->> 隐藏服务器:RPO节点信息
note over 隐藏服务器 :与RPO建链
客户端 ->> 隐藏服务器: request
隐藏服务器 ->> 客户端:reply

```



![image-20230214215857508](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230214215858.png)

![image-20230214220827295](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230214220828.png)

Tor客户端，从目录服务器下载并解析共识文档，从中可以解析出所有中继节点信息

中继节点信息

![image-20230214221343993](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230214221344.png)

用户选择三个节点，中继节点层层加密信息

![image-20230214222522192](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230214222522.png)

连接建立过程：

通过DH算法交换中间节点的密钥K，建立过程为（来自：[1]王付刚. TOR匿名通信技术研究[D].哈尔滨工业大学,2010.）

![image-20230215221850571](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230215221851.png)

![image-20230215215258800](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230215215259.png)

匿名通信主要过程

1. tor代理从目录服务器获取路由节点信息
2. Tor代理使用**路由选择算法**选择3个路由节点建立电路，并通过Diffie-Hellman算法分别与这三个节点生成共享密钥K1，K2，K3。根据位置，称为入口节点，中间节点，出口节点（Exit）
3. Tor代理依次使用K3，K2，K1对消息M进行加密，路由节点依次使用K1，k2，k3进行解密，最终将原文发送给目标站点

``` mermaid
graph LR
Tor代理 --"E(K1,M)"-->Entry
Entry--"D(K1,M),E(K2,M)" -->Middle
Middle--"D(K2,M),E< K3,M >" -->Exit
```

``` mermaid
sequenceDiagram
autonumber
participant Tor代理
Note over Tor代理:依次使用K3，k2，k1对明文M以及各跳的地址进行加密,得到密文C
Tor代理 ->> Entry:发送加密后的密文C
Note over Entry:使用K1进行解密，获得密文C1和Middle的地址
Entry ->> Middle:发送密文c1
note over Middle:使用k2对c1进行解密，获得密文C2和Exit的地址
Middle ->> Exit:发送密文C2
note over Exit:使用k3对密文c2进行解密，获得明文M和目标站点地址
Exit ->> Server:明文M
```

通过使用circleID可以唯一的识别一段链路，这样通过<entry,output,circleID>这样的形式，server就可以将消息会送给客户端。服务器也不知道具体的内容和ip。



疑问：

路由节点怎么记录上一跳和下一跳的地址

参考：

[1]王付刚. TOR匿名通信技术研究[D].哈尔滨工业大学,2010.
