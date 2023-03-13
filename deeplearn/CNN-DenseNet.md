## 1. DenseNet的基本介绍

## 2. 基本结构

### 1. 结构模式图

### 2. 瓶颈层
  瓶颈层是组成DenseBlock的成分之一。之所以称之为瓶颈层，因为在神经网络各层的feature map中，瓶颈层的feature map就像玻璃瓶的瓶颈一样。由于后面层的输入会很大，因此使用BottleNeck层来实现数据降维。通常先使用1×1的卷积核进行降维，然后使用3×3的卷积核再次降维。如果直接用一个3×3的卷积核一步到位，则参数和计算量都会很大。

### 1. 瓶颈层的基本结构
``` mermaid
graph LR
bn1[BN]-->r1[relu]
r1-->conv1[size=1*1,filters=4k]
conv1-->bn2
bn2[BN]-->r2[relu]
r2-->conv2[size=3*3,filters=k]
```


## 3. 特点

### 1. Densely Connected

前面的每一层的输出都会作为后面每一层数输入，假设一共有L层的卷积神经网络，不同于传统神经网络的线性连接，输入层一共与后面L层均有连接，共L个，其后为等差数列排列，使用等差数列求和公式有，DenseNet各层之间的连接数一共有：
$$
connections=\frac {L(L+1)}{2}
$$
