	GoogleNet通过**稀疏性**提高神经网络的表现，而不像ALexNet和VGG网络通过提高网络的深度提高网络性能。

##	1. GoogleNet的基本介绍

​	GoogleNet增加了**卷积模块**功能，他将相关性强的特征先聚集在一起，每一种尺寸的卷积输出都作为总输出的一部分。通过Inception模块，保持了网络结构的稀疏性，又不降低性能。

##	2. GoogleNet基本思想
  使用Inception 结构，加宽网络。在隐藏层不使用全连接层，减少参数。
  - 相当于做集成学习，在不同模块处进行输出，再加上全连接层进行分类。在不同层次做分类，相当于集成模型。在特征粗略，细腻时都进行分类。

##	3. Inception模块
  使用不同尺寸的卷机核，大的卷积核损失特征多，因此可以使用小卷积核进行补偿。将不同特征图使用最大化池化进行**特征融合**，module之间进行级连。虽然层次加深和结构复杂，但是在同样网络规模下，连接数增加缓慢。

### 1. V1

![image-20220417164449915](C:\Users\23860\AppData\Roaming\Typora\typora-user-images\image-20220417164449915.png)
使用1×1的卷积作用：
大大减小权重的数量，特征图的大小不变，但是仍然可以获取某个方向的特征。减小了计算复杂度
使用混合卷积，卷积核大小不同，相当于增加了通道数，细化了特征提取。卷积核越大，特征获取越粗。1×1的也可以作为特征补偿，减小特征损失。

输出可以通过旁路输出，提取测试结果好不好。在不同的阶段做分类，相当于做集成学习。

### 2. V2

### 3. V3

###  4. V4

### 5.Inception-residual



##	4. GoogleNet基本结构
