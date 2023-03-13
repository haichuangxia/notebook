基本思路

1. 初始时发送包号为0的数据包,创建connection
2. 客户端发送消息请求数据,server根据请求创建stream,stream管理数据

一旦有数据发送,则调用sendMessage()方法发送数据.