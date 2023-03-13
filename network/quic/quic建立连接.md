# quic协议层次结构

![image-20221213132239438](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221213132241.png)

![image-20221213133511556](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221213133512.png)

## 四种包及其包保护

![image-20221213143041946](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221213143043.png)

![image-20221213155237811](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221213155239.png)

![image-20221213155256148](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221213155257.png)

![image-20221213160251726](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221213160252.png)

加密等级→独立的secret值→使用KDF算法根据TLS secret值计算packet protection密钥