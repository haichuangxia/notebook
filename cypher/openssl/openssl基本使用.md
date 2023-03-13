# OpenSSL基本介绍

***OpenSSL***是一个密码学的相关库,提供了两个库***libssl***和***libcrypto***.其囊括了主要的:*密码算法*,*密钥和证书封装管理*以及*SSL*协议.

| 组成部分  | 功能                                  |
| --------- | ------------------------------------- |
| libcrypto | 加密算法库,包括opensssl-libs          |
| libssl    | 加密模块应用库,实现了ssl以及tls,包nss |

很多其他的工具都使用***OpenSSL***库,比如***OpenSSH***,其使用了***OpenSSL***提供的***libcrypto***库来实现一些密码学功能.

# Overview

| 功能 | 命令 | 作用 |
| ---- | ---- | ---- |
| 签名 |      |      |
|      |      |      |



# OpenSSL的基本功能

| 命令   | 作用     |
| ------ | -------- |
| genrsa | 生成密钥 |
| rsa    | 加密文件 |
| reault | 解密文件 |



## 数据加密

```
openssl enc ciphertype -e -in plain.txt -out cipher.bin \
-K 00112233445566778899aabbccddeeff \
-iv 0102030405060708
```

ciphertype:加密模式，主要包括密码算法和加密模式，如`-aes-128-cbc`

| 参数            | 含义           |
| --------------- | -------------- |
| cipher          | aes，des等算法 |
| encryption mode | cbc，ecb等模式 |



部分选项的主要含义：

| 参数   | 含义                               | 含义(中文)             |
| ------ | ---------------------------------- | ---------------------- |
| -in    | `<file>` input file                | 输入文件               |
| -out   | `<file>` output file               | 输出文件               |
| -e     | encrypt                            | 加密                   |
| -d     | decrypt                            | 解密                   |
| -K/-iv | key/iv in hex is the next argument | 十六进制密钥与初始向量 |
| -[pP]  | print the iv/key (then exit if -P) | 打印初始向量与密钥     |



# 使用OpenSSL加密库进行编程

编译：

```
gcc -I /usr/include/openssl -L /usr/lib/ssl -o enc fileKey.c -lcrypto -ldl
```

解释：

| 参数    | 意义                 |      |
| ------- | -------------------- | ---- |
| -I      | h头文件所在位置      |      |
| -L      | .o库文件文件所在目录 |      |
| -l+库名 | 要连接的.o库文件名称 |      |



参考:

1. [openssl用法简介_ltgsoldier1的博客-CSDN博客_openssl 用法](https://blog.csdn.net/ltgsoldier1/article/details/128322870?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2~default~YuanLiJiHua~Position-3-128322870-blog-107891790.pc_relevant_recovery_v2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~YuanLiJiHua~Position-3-128322870-blog-107891790.pc_relevant_recovery_v2&utm_relevant_index=6)
