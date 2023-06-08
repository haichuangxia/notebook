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

# openssl对称加密

注意两点

1. 加密明文时，明文长度以`strlen()`函数计算为准，而不是字符数组的长度，`openssl`实现中不加密’\0‘。
2. 解密时，输入的密文长度必须时16的整数倍
3. 加密时，如果输入的明文不是16的整数倍，则会使用默认的方式进行填充。因此解密时，输出的是进行填充后的明文。

``` c
// 注意：此处将要加密的明文是字符串，第二个参数是要加密的字符串长度，该字符串长度以strlen（）函数来计算，遇到'\0'停止
int encrypt(unsigned char *plaintext, int plaintext_len, unsigned char *key,
            unsigned char *iv, unsigned char *ciphertext)
{
    if (!plaintext || !plaintext_len || !key)
    {
        printf("要解密的明文或需要使用的密钥为空。\n");
        return -1;
    };
    EVP_CIPHER_CTX *ctx;

    int len;

    int ciphertext_len;

    /* Create and initialise the context */
    if (!(ctx = EVP_CIPHER_CTX_new()))
        handleErrors();

    /*
     * Initialise the encryption operation. IMPORTANT - ensure you use a key
     * and IV size appropriate for your cipher
     * In this example we are using 256 bit AES (i.e. a 256 bit key). The
     * IV size for *most* modes is the same as the block size. For AES this
     * is 128 bits
     */
    if (1 != EVP_EncryptInit_ex(ctx, EVP_aes_128_cbc(), NULL, key, iv))
        handleErrors();

    /*
     * Provide the message to be encrypted, and obtain the encrypted output.
     * EVP_EncryptUpdate can be called multiple times if necessary
     */
    if (1 != EVP_EncryptUpdate(ctx, ciphertext, &len, plaintext, plaintext_len))
        handleErrors();
    ciphertext_len = len;

    /*
     * Finalise the encryption. Further ciphertext bytes may be written at
     * this stage.
     */
    if (1 != EVP_EncryptFinal_ex(ctx, ciphertext + len, &len))
        handleErrors();
    ciphertext_len += len;

    /* Clean up */
    EVP_CIPHER_CTX_free(ctx);

    return ciphertext_len;
}
```



# aes的填充模式

根据明文的长度，可以选择是否进行填充

- blocksize的整数倍：可以不填充
- blocksize的非整数倍：必须进行填充

值得注意的是，如果使用填充，即便是blocksize的整数倍，也有可能会进行填充。

