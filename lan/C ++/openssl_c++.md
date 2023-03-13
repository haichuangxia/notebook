c++:调用OpensSSL库来进行加密



# 介绍

## Open SSL EVP

EVP是封装的高层接口，通过它加解密，可以不用关心更多细节问题，使用更简单。通过这样的统一的封装，使得只需要在初始化参数的时候做很少的改变，就可以使用相同的代码但采用不同的加密算法进行数据的加密和解密。

使用EVP的好处就是，不用考虑诸如对齐填充、秘钥、处理长度等等细节

## EVP的几个数据结构

| 数据结构       | 含义                                                         |
| -------------- | ------------------------------------------------------------ |
| EVP_PKEY       | 存放非对称密钥信息                                           |
| EVP_MD         | 存放摘要算法信息、非对称算法类型以及各种计算函数             |
| EVP_CIPHER     | 该结构用来存放对称加密相关的信息以及算法                     |
| EVP_CIPHER_CTX | 对称算法上下文结构，此结构主要用来维护加解密状态，存放中间以及最后结果 |



## AES算法

AES是分组密码,

## EVP中对称加密解密流程

``` mermaid
graph TD
EVP_CIPHER_CTX_init --> EVP_EncryptInit_ex
EVP_EncryptInit_ex --> EVP_EncryptUpdate
EVP_EncryptUpdate --> EVP_EncryptFinal_ex
```

