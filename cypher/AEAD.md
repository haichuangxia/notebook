AEAD：Authenticated Encryption with Associated Data（附带关联数据的认证加密）

目的：对称加密且可以验证解密步骤是否正确

# 简单的加密+验证的AEAD方案

## EtM（**Encryption then MAC**）

![etm](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230216104212.webp)

1. 发送方对密文进行hash运算，获取mac与密文一同发送
2. 接收方再次对密文进行hash运算，验证两个mac是否相同后再解密

## E&M**(Encryption and MAC)**

![img](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230216104539.webp)

1. 发送方同时对原文进行加密和hash运算，
2. 接收方先解密，获取明文再hash运算，比较两个值是否相同来验证是否解密正确

## **MtE (MAC then Encryption)**

![img](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2023/20230216104716.webp)

1. 发送方先hash运算，然后再加密
2. 接收方先解密，然后hash运算，验证解密结果是否正确

# AEAD算法标准方法

## 介绍

传统的三种方法通过组合加密和认证算法实现AEAD算法有安全问题，新的标准的aead算法通过在算法内部同时实现加密和认证使其成为真正的aead算法。

常见的aead标准实现算法有：

| Name                   | Alias                  | Key Size | Salt Size | Nonce Size | Tag Size |
| :--------------------- | :--------------------- | :------- | :-------- | :--------- | :------- |
| AEAD_CHACHA20_POLY1305 | chacha20-ietf-poly1305 | 32       | 32        | 12         | 16       |
| AEAD_AES_256_GCM       | aes-256-gcm            | 32       | 32        | 12         | 16       |
| AEAD_AES_192_GCM       | aes-192-gcm            | 24       | 24        | 12         | 16       |
| AEAD_AES_128_GCM       | aes-128-gcm            | 16       | 16        | 12         | 16       |



## openssl中的aead算法

